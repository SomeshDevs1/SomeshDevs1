As discussed earlier, I have committed to delivering the SGIAM changes up to the dev merge stage.

However, we have encountered a new blocker: certain existing methods (e.g., getTeamUserDetails) are still dependent on the User VAC table for fetching user details. With the SGIAM integration, this dependency needs to be removed or replaced with the new token-based approach. This change requires additional analysis and refactoring, which is impacting our ability to complete the delivery by Monday.

I am currently assessing the impact of this dependency across the affected methods and will coordinate with the relevant teams to identify the best approach for migration. I will keep you posted on progress and timelines.