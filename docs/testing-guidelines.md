# Testing Guidelines

This document outlines the testing strategy and guidelines for the Todo application, following the testing pyramid approach.

## Testing Pyramid

The testing pyramid is a framework that helps create a balanced test suite with the right mix of unit tests, integration tests, and end-to-end tests.

```
       /\
      /  \     E2E Tests (Few)
     /----\
    /      \   Integration Tests (Some)
   /--------\
  /          \ Unit Tests (Many)
 /____________\
```

### Testing Distribution
- **Unit Tests**: 70% - Fast, isolated, focus on individual functions/methods
- **Integration Tests**: 20% - Test component interactions and integrations
- **E2E Tests**: 10% - Test complete user workflows from start to finish

## Unit Testing

### Purpose
Unit tests verify that individual units of code (functions, methods, classes) work correctly in isolation.

### Guidelines

**What to Test**:
- Pure functions and utility methods
- Business logic and data transformations
- Input validation and error handling
- State management logic
- Individual React components (isolated)
- API route handlers and controllers
- Data models and services

**Best Practices**:
- Write tests for each testable piece of code
- Test one thing per test case
- Use descriptive test names that explain what is being tested
- Follow the AAA pattern: Arrange, Act, Assert
- Mock external dependencies (APIs, databases, third-party services)
- Aim for high code coverage (80%+ for critical code)
- Keep tests fast (milliseconds per test)
- Make tests independent and repeatable

**Example Test Cases**:
- ✓ Function returns correct output for valid input
- ✓ Function handles edge cases (empty arrays, null values, etc.)
- ✓ Function throws appropriate errors for invalid input
- ✓ Component renders correctly with given props
- ✓ Component updates state when user interacts
- ✓ API handler validates request data

**Tools**:
- **Jest**: Primary testing framework
- **React Testing Library**: For React component testing
- **Supertest**: For testing Express API endpoints

## Integration Testing

### Purpose
Integration tests verify that different parts of the application work correctly together.

### Guidelines

**What to Test**:
- Component interactions and communication
- Data flow between components
- Integration with APIs and services
- Database operations
- State management with multiple components
- Form submissions and data validation
- Authentication and authorization flows

**Best Practices**:
- Test realistic user scenarios
- Test component hierarchies and prop passing
- Verify API endpoints with actual HTTP requests
- Test database queries with a test database
- Mock only external services (third-party APIs)
- Test error handling and edge cases
- Ensure proper cleanup after each test

**Example Test Cases**:
- ✓ Todo list component fetches and displays tasks from API
- ✓ Creating a task updates both the UI and database
- ✓ Form validation prevents invalid data submission
- ✓ Error messages display when API requests fail
- ✓ Authentication protects restricted routes
- ✓ Multiple components work together in a feature

**Tools**:
- **Jest**: Testing framework
- **React Testing Library**: For component integration
- **Supertest**: For API integration testing
- **MSW (Mock Service Worker)**: For mocking external APIs

## End-to-End (E2E) Testing

### Purpose
E2E tests verify complete user workflows from start to finish, testing the application as a whole.

### Guidelines

**What to Test**:
- Critical user journeys and workflows
- Complete feature flows from UI to database
- Cross-browser compatibility
- Real user scenarios with realistic data
- Application behavior in production-like environment

**Test Flows**:

#### 1. Task Creation Flow
1. User opens the application
2. User clicks "Add Task" button
3. User enters task details
4. User submits the form
5. Verify task appears in the list
6. Verify task is saved to database

#### 2. Task Update Flow
1. User views existing task list
2. User clicks on a task to edit
3. User modifies task details
4. User saves changes
5. Verify updated task displays correct information
6. Verify changes persist after page reload

#### 3. Task Deletion Flow
1. User views task list with existing tasks
2. User clicks delete button on a task
3. User confirms deletion (if confirmation is required)
4. Verify task is removed from the list
5. Verify task is deleted from database

#### 4. Complete User Journey
1. User opens application for the first time
2. User creates multiple tasks
3. User marks some tasks as complete
4. User edits a task
5. User deletes a task
6. User filters or sorts tasks
7. Verify all operations work correctly together

**Best Practices**:
- Focus on critical user paths
- Test happy paths and important error scenarios
- Use realistic test data
- Run tests in isolation (clean state for each test)
- Keep E2E tests maintainable and not brittle
- Use stable selectors (data-testid attributes)
- Minimize test dependencies and flakiness
- Run E2E tests against a staging environment

**Tools**:
- **Cypress**: Modern E2E testing framework (recommended)
- **Playwright**: Alternative E2E testing framework
- **Puppeteer**: Headless browser automation

## Testing Standards

### Code Coverage
- **Overall Coverage Target**: 80%+
- **Critical Code Coverage**: 90%+ (business logic, data handling)
- **Acceptable Coverage**: 70%+ (UI components, simple utilities)

### Test Organization
```
packages/
  backend/
    __tests__/
      unit/
        services/
        models/
        utils/
      integration/
        api/
        database/
  frontend/
    src/
      __tests__/
        unit/
          components/
          utils/
        integration/
          features/
    e2e/
      specs/
        tasks.spec.js
        user-flows.spec.js
```

### Naming Conventions
- **Test Files**: `*.test.js` or `*.spec.js`
- **Test Suites**: Use `describe()` blocks with clear names
- **Test Cases**: Use `it()` or `test()` with descriptive names
- **Example**: `describe('TaskService', () => { it('should create a new task', () => {...}) })`

### Continuous Integration
- Run all unit tests on every commit
- Run integration tests on pull requests
- Run E2E tests before merging to main branch
- Block merges if tests fail
- Monitor test execution time and optimize slow tests

## Test Data Management

### Test Data Guidelines
- Use factories or fixtures for consistent test data
- Generate realistic test data
- Clean up test data after each test
- Use separate test databases
- Avoid hardcoding test data when possible

### Mocking Strategy
- Mock external APIs and services
- Use real implementations for internal code
- Create reusable mock factories
- Document mock behavior clearly

## Accessibility Testing

### Testing for WCAG Compliance
- Include accessibility tests in unit and integration tests
- Use automated accessibility testing tools (jest-axe, axe-core)
- Test keyboard navigation in E2E tests
- Verify screen reader compatibility
- Check color contrast ratios

## Performance Testing

### Performance Considerations
- Monitor test execution time
- Identify and optimize slow tests
- Consider performance tests for critical operations
- Test application behavior under load (optional)

## Best Practices Summary

1. **Write Tests First (TDD)**: Consider test-driven development for critical features
2. **Keep Tests Simple**: Each test should be easy to understand
3. **Test Behavior, Not Implementation**: Focus on what the code does, not how
4. **Maintain Tests**: Update tests when requirements change
5. **Run Tests Frequently**: Run unit tests during development, all tests before commits
6. **Review Test Coverage**: Regularly check coverage reports
7. **Document Complex Tests**: Add comments for non-obvious test logic
8. **Avoid Test Interdependence**: Each test should run independently
9. **Use Descriptive Assertions**: Make test failures easy to diagnose
10. **Balance Speed and Coverage**: Fast tests encourage frequent running

## Getting Started

### Running Tests

```bash
# Run all unit tests
npm test

# Run tests in watch mode
npm test -- --watch

# Run tests with coverage
npm test -- --coverage

# Run integration tests
npm run test:integration

# Run E2E tests
npm run test:e2e
```

### Writing Your First Test

```javascript
// Example unit test
describe('addTask', () => {
  it('should add a new task with a unique ID', () => {
    // Arrange
    const task = { title: 'Test Task', description: 'Test Description' };
    
    // Act
    const result = addTask(task);
    
    // Assert
    expect(result).toHaveProperty('id');
    expect(result.title).toBe('Test Task');
  });
});
```

## Resources

- [Jest Documentation](https://jestjs.io/)
- [React Testing Library](https://testing-library.com/react)
- [Cypress Documentation](https://docs.cypress.io/)
- [Testing Best Practices](https://testingjavascript.com/)
- [Testing Pyramid by Martin Fowler](https://martinfowler.com/articles/practical-test-pyramid.html)
