List<String> extractedValues = new ArrayList<>();

if (userAuthList != null) {
    for (Map<String, Object> authEntry : userAuthList) {
        List<Map<String, Object>> permissions = (List<Map<String, Object>>) authEntry.get("permissions");
        if (permissions != null) {
            for (Map<String, Object> permission : permissions) {
                List<Map<String, Object>> constraints = (List<Map<String, Object>>) permission.get("constraints");
                if (constraints != null) {
                    for (Map<String, Object> constraint : constraints) {
                        List<String> values = (List<String>) constraint.get("values");
                        if (values != null) {
                            extractedValues.addAll(values); // e.g., SUP EUR, etc.
                        }
                    }
                }
            }
        }
    }
}