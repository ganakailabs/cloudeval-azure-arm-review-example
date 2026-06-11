# Passing baseline review demo

This branch demonstrates a CloudEval review that is allowed to pass while still
showing the current architecture posture in the pull request comment.

The intent is not to claim that the architecture is excellent. It shows a common
adoption pattern:

1. Connect the repository to CloudEval.
2. Capture the current Well-Architected, cost, and validation baseline.
3. Keep pull request feedback visible.
4. Tighten `ci.gates` thresholds after remediation work begins.

Use this pattern for early rollout or non-production repositories. For production
environments, keep `fail_on_high_risk` and `fail_on_validation_errors` enabled
once the initial baseline is clean.
