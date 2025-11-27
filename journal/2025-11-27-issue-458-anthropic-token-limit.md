# Issue #458 - Anthropic Token Limit Fix

**Date**: 2025-11-27 07:00-07:10 UTC
**Session**: Autonomous Run
**Task**: Fix issue #458 - "Anthropic prompt is too long"
**Status**: ✅ COMPLETE - PR created

## Problem Analysis

User reported crash after long conversation:
anthropic.BadRequestError: prompt is too long: 200297 tokens > 200000 maximum

**Root Cause Identified:**
1. Anthropic models have 200k token context limit
2. Reduction logic triggers at 0.9 × 200k = 180k tokens
3. Token counting uses tiktoken's cl100k_base as fallback for Anthropic models
4. This fallback **undercounts** tokens for Claude models (~11% error)
5. System thinks it's at ~180k when actually at 200k+
6. API call fails when actual count exceeds limit

## Investigation Steps

1. Read issue #458 and Erik's comment about reduction strategies
2. Examined `gptme/util/reduce.py` - reduction logic exists
3. Traced token counting to `gptme/util/tokens.py`
4. Found tiktoken fallback to cl100k_base for unknown models
5. Identified inaccuracy as root cause

## Solution Implemented

Modified `gptme/util/reduce.py` to use conservative multiplier for Anthropic models:
- **Before**: 0.9 × 200k = 180k trigger point
- **After**: 0.75 × 200k = 150k trigger point

This provides 50k token safety margin to account for tokenizer undercounting.

## Changes Made

- Modified reduction trigger calculation in `reduce_log()` function
- Added model detection: `"anthropic" in model.model.lower()`
- Applied 0.75 multiplier for Anthropic, 0.9 for others
- Added explanatory comments documenting the issue

## Verification

- Existing tests pass (use GPT-4, not affected by change)
- Logic prevents reported error by triggering earlier
- Still utilizes 75% of context window

## Deliverables

- **PR #887**: https://github.com/gptme/gptme/pull/887
- **Issue Comment**: Explained root cause and fix
- **Branch**: fix/anthropic-token-limit

## Outcome

✅ High-priority bug fixed with minimal, targeted change
✅ Prevents API errors for Anthropic users in long conversations
✅ Maintains good context window utilization (75%)
✅ No impact on other model providers
