@Test
public void testGetTaskConfiguration_EmptyFundCodes() {
    // Setup
    List<Integer> fundCodes = Collections.emptyList();
    String perimeter = "some_perimeter";
    TaskConfigurationDTO taskConfig = new TaskConfigurationDTO();

    // Mock behavior (if needed)
    when(mockTaskConfigUtil.getFundRules(any(), any())).thenReturn(...);

    // Execute
    TaskConfigurationDTO result = yourService.getTaskConfiguration(fundCodes, perimeter);

    // Verify
    assertNotNull(result);
    // Additional assertions based on what the function should return in this case
}


@Test
public void testGetTaskConfiguration_SingleFundCode() {
    // Setup
    List<Integer> fundCodes = Collections.singletonList(123);
    String perimeter = "LinkFundConstants.WFS";
    TaskConfigurationDTO taskConfig = new TaskConfigurationDTO();

    // Mock behavior (if needed)
    when(mockTaskConfigUtil.getFundRules(any(), any())).thenReturn(...);

    // Execute
    TaskConfigurationDTO result = yourService.getTaskConfiguration(fundCodes, perimeter);

    // Verify
    assertNotNull(result);
    // Assertions based on expected behavior
}

@Test
public void testGetTaskConfiguration_MultipleFundCodes() {
    // Setup
    List<Integer> fundCodes = Arrays.asList(123, 456);
    String perimeter = "LinkFundConstants.ANOTHER_CONSTANT";
    TaskConfigurationDTO taskConfig = new TaskConfigurationDTO();

    // Mock behavior (if needed)
    when(mockTaskConfigUtil.getFundRules(any(), any())).thenReturn(...);

    // Execute
    TaskConfigurationDTO result = yourService.getTaskConfiguration(fundCodes, perimeter);

    // Verify
    assertNotNull(result);
    // Assertions based on expected behavior
}

