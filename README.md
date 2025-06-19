@Override
public UserDto getUserDetails(Authentication authentication, Integer id) throws VAPServiceException {
    try {
        OAuth2AuthenticatedPrincipal user = (OAuth2AuthenticatedPrincipal) authentication.getPrincipal();

        // Extracting fields
        String sesame = user.getAttribute(USER_SESAME_FIELD_IN_CLAIMS);
        String firstName = user.getAttribute("first_name");
        String lastName = user.getAttribute("family_name");
        String quadrigram = user.getAttribute("name"); // assuming this is full name or ID
        String loginEmail = user.getAttribute("login_ad");

        if (StringUtils.isNotEmpty(sesame)) {
            // Optionally fetch userVac from DB using sesame
            // UserVac userVac = userVacRepository.findUserVacBySesameCode(sesame);

            // Temporarily, build UserDto directly with auth info
            return new UserDto(
                id,
                sesame,
                lastName,
                firstName,
                quadrigram,
                new ArrayList<>(), // placeholder for userGroup
                new ArrayList<>(), // placeholder for userModule
                loginEmail
            );
        } else {
            throw new VAPServiceException("User not available");
        }
    } catch (Exception e) {
        LOGGER.error("Error Fetching user details", e);
        throw new VAPServiceException("Error Fetching user details");
    }
}