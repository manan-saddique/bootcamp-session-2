# Coding Guidelines

This document outlines the coding principles and best practices for the Todo application.

## Core Principles

### DRY (Don't Repeat Yourself)

**Principle**: Avoid duplicating code or logic. Each piece of knowledge should have a single, unambiguous representation in the system.

**Guidelines**:
- Extract repeated code into reusable functions or components
- Create utility functions for common operations
- Use configuration objects instead of hardcoded values
- Share common logic through modules or services
- Avoid copy-paste programming

**Examples**:

❌ **Bad - Repetitive Code**:
```javascript
function calculateTotalActive() {
  let total = 0;
  for (let i = 0; i < tasks.length; i++) {
    if (tasks[i].status === 'active') {
      total++;
    }
  }
  return total;
}

function calculateTotalCompleted() {
  let total = 0;
  for (let i = 0; i < tasks.length; i++) {
    if (tasks[i].status === 'completed') {
      total++;
    }
  }
  return total;
}
```

✅ **Good - DRY Implementation**:
```javascript
function countTasksByStatus(tasks, status) {
  return tasks.filter(task => task.status === status).length;
}

const activeCount = countTasksByStatus(tasks, 'active');
const completedCount = countTasksByStatus(tasks, 'completed');
```

### SOLID Principles

SOLID is an acronym for five design principles that make software more maintainable and scalable.

#### S - Single Responsibility Principle (SRP)
**Principle**: A class or function should have one, and only one, reason to change.

**Guidelines**:
- Each function should do one thing well
- Each component should have a single, well-defined purpose
- Separate concerns (UI, business logic, data access)
- Break down large functions into smaller, focused ones

**Example**:
```javascript
// ❌ Bad - Multiple responsibilities
function handleTaskSubmit(taskData) {
  // Validation
  if (!taskData.title) throw new Error('Title required');
  
  // Business logic
  const task = { ...taskData, id: generateId(), createdAt: Date.now() };
  
  // API call
  fetch('/api/tasks', { method: 'POST', body: JSON.stringify(task) });
  
  // UI update
  updateTaskList(task);
}

// ✅ Good - Single responsibilities
function validateTask(taskData) {
  if (!taskData.title) throw new Error('Title required');
}

function createTask(taskData) {
  return { ...taskData, id: generateId(), createdAt: Date.now() };
}

async function saveTask(task) {
  return await fetch('/api/tasks', { method: 'POST', body: JSON.stringify(task) });
}

function handleTaskSubmit(taskData) {
  validateTask(taskData);
  const task = createTask(taskData);
  saveTask(task).then(updateTaskList);
}
```

#### O - Open/Closed Principle
**Principle**: Software entities should be open for extension but closed for modification.

**Guidelines**:
- Design components that can be extended without modifying existing code
- Use composition over inheritance
- Leverage props, callbacks, and configuration options
- Use plugins or middleware patterns where appropriate

#### L - Liskov Substitution Principle
**Principle**: Objects should be replaceable with instances of their subtypes without affecting functionality.

**Guidelines**:
- Ensure derived classes maintain the behavior of base classes
- Follow interface contracts
- Don't remove or weaken functionality in derived classes

#### I - Interface Segregation Principle
**Principle**: Clients should not be forced to depend on interfaces they don't use.

**Guidelines**:
- Keep interfaces small and focused
- Split large interfaces into smaller, specific ones
- Use props destructuring to only accept needed properties

#### D - Dependency Inversion Principle
**Principle**: Depend on abstractions, not on concrete implementations.

**Guidelines**:
- Inject dependencies rather than hardcoding them
- Use dependency injection patterns
- Mock dependencies in tests
- Separate interface from implementation

**Example**:
```javascript
// ✅ Good - Dependency injection
class TaskService {
  constructor(apiClient, logger) {
    this.apiClient = apiClient;
    this.logger = logger;
  }
  
  async createTask(taskData) {
    this.logger.info('Creating task');
    return await this.apiClient.post('/tasks', taskData);
  }
}

// Easy to test with mocks
const mockApi = { post: jest.fn() };
const mockLogger = { info: jest.fn() };
const service = new TaskService(mockApi, mockLogger);
```

## Code Quality

### Linting

**Purpose**: Enforce consistent code style and catch potential errors early.

**Configuration**:
- Use **ESLint** for JavaScript/TypeScript linting
- Configure linting rules appropriate for the project
- Use **Prettier** for code formatting
- Enable linting in pre-commit hooks
- Run linting in CI/CD pipeline

**Required Tools**:
```bash
npm install --save-dev eslint prettier eslint-config-prettier
```

**Best Practices**:
- Fix linting errors before committing code
- Don't disable linting rules without good reason
- Document any rule exceptions in comments
- Keep linting configuration consistent across the team
- Run linting automatically on file save (IDE configuration)

**ESLint Configuration Example** (`.eslintrc.js`):
```javascript
module.exports = {
  extends: ['eslint:recommended', 'prettier'],
  env: {
    browser: true,
    node: true,
    es2021: true,
    jest: true
  },
  rules: {
    'no-console': 'warn',
    'no-unused-vars': 'error',
    'prefer-const': 'error',
    'no-var': 'error'
  }
};
```

### Code Formatting

**Standards**:
- Use consistent indentation (2 or 4 spaces, not tabs)
- Use semicolons consistently (either always or never)
- Use single quotes for strings (unless template literals needed)
- Break long lines (max 80-100 characters)
- Add spaces around operators and after commas
- Use meaningful line breaks to group related code

**Prettier Configuration** (`.prettierrc`):
```json
{
  "singleQuote": true,
  "trailingComma": "es5",
  "tabWidth": 2,
  "semi": true,
  "printWidth": 80
}
```

**Best Practices**:
- Let Prettier handle formatting automatically
- Use consistent file organization
- Add blank lines between logical sections
- Group imports logically (external, internal, utilities)

## Keep It Simple

### Avoid Complex Logic

**Principle**: Write simple, readable code that is easy to understand and maintain.

**Guidelines**:
- Break complex operations into smaller steps
- Use descriptive variable and function names
- Limit function complexity (cyclomatic complexity < 10)
- Avoid deeply nested conditionals (max 3 levels)
- Prefer early returns over nested if-else
- Use clear conditional expressions

**Examples**:

❌ **Bad - Complex nested logic**:
```javascript
function processTask(task) {
  if (task) {
    if (task.status === 'active') {
      if (task.priority === 'high') {
        if (task.dueDate && task.dueDate < Date.now()) {
          return 'overdue-high-priority';
        } else {
          return 'active-high-priority';
        }
      } else {
        return 'active-normal';
      }
    } else {
      return 'inactive';
    }
  }
  return null;
}
```

✅ **Good - Simple with early returns**:
```javascript
function processTask(task) {
  if (!task) return null;
  if (task.status !== 'active') return 'inactive';
  
  const isHighPriority = task.priority === 'high';
  const isOverdue = task.dueDate && task.dueDate < Date.now();
  
  if (isHighPriority && isOverdue) return 'overdue-high-priority';
  if (isHighPriority) return 'active-high-priority';
  
  return 'active-normal';
}
```

**Readability Tips**:
- One level of abstraction per function
- Use guard clauses (early returns)
- Extract complex conditions into named variables
- Prefer declarative over imperative code
- Use array methods (map, filter, reduce) appropriately

### Avoid Over-Engineering

**Principle**: Solve today's problems, not hypothetical future ones. Build what you need, when you need it.

**Guidelines**:
- Implement requirements as specified, don't add extra features
- Don't create abstractions until you need them (Rule of Three: abstract after third repetition)
- Avoid premature optimization
- Use simple solutions over clever ones
- Don't build frameworks when libraries will do
- Start simple and refactor when complexity is justified

**Common Over-Engineering Mistakes**:
- ❌ Creating a complex plugin system for a simple feature
- ❌ Adding caching before measuring performance
- ❌ Building configuration systems for hardcoded values
- ❌ Creating elaborate class hierarchies for simple objects
- ❌ Implementing design patterns without clear need

**YAGNI (You Aren't Gonna Need It)**:
- Don't add functionality until it's necessary
- Remove unused code and dependencies
- Resist the urge to add "just in case" features
- Focus on current requirements

**Examples**:

❌ **Bad - Over-engineered**:
```javascript
// Complex abstraction for simple operations
class TaskRepositoryFactory {
  static create(type) {
    switch(type) {
      case 'api': return new ApiTaskRepository();
      case 'local': return new LocalTaskRepository();
      case 'cache': return new CachedTaskRepository();
      default: throw new Error('Unknown type');
    }
  }
}

class TaskServiceBuilder {
  constructor() {
    this.repository = null;
    this.validator = null;
    this.logger = null;
  }
  
  withRepository(repo) { this.repository = repo; return this; }
  withValidator(val) { this.validator = val; return this; }
  withLogger(log) { this.logger = log; return this; }
  
  build() {
    return new TaskService(this.repository, this.validator, this.logger);
  }
}
```

✅ **Good - Simple and direct**:
```javascript
// Simple, straightforward implementation
async function getTasks() {
  const response = await fetch('/api/tasks');
  return response.json();
}

async function createTask(taskData) {
  const response = await fetch('/api/tasks', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(taskData)
  });
  return response.json();
}
```

**When to Abstract**:
- Same code appears in 3+ places (Rule of Three)
- Clear pattern emerges across multiple use cases
- Abstraction simplifies rather than complicates
- Testing becomes easier with abstraction

## Naming Conventions

### General Guidelines
- Use **descriptive, meaningful names**
- **Be specific**: `getUserById` not `getData`
- **Avoid abbreviations** unless widely known (e.g., `id`, `url`, `api`)
- **Use pronounceable names**
- **Avoid mental mapping** (single-letter variables except in loops)

### JavaScript/TypeScript Conventions
- **Variables & Functions**: camelCase (`taskList`, `createTask`)
- **Classes & Components**: PascalCase (`TaskService`, `TodoList`)
- **Constants**: UPPER_SNAKE_CASE (`MAX_TASKS`, `API_URL`)
- **Private properties**: prefix with underscore (`_privateMethod`)
- **Boolean variables**: use `is`, `has`, `can` prefixes (`isActive`, `hasError`)

## Comments and Documentation

### When to Comment
- **Do**: Explain *why*, not *what*
- **Do**: Document complex algorithms or business logic
- **Do**: Add JSDoc for public APIs and functions
- **Don't**: State the obvious
- **Don't**: Use comments to explain bad code (refactor instead)

**Examples**:

❌ **Bad - Obvious comment**:
```javascript
// Increment i by 1
i++;

// Loop through tasks
for (const task of tasks) { ... }
```

✅ **Good - Explains why**:
```javascript
// Use exponential backoff to avoid overwhelming the API
// during retry attempts after network failures
const delay = Math.pow(2, retryCount) * 1000;

/**
 * Calculates task priority score based on urgency and importance
 * using the Eisenhower Matrix methodology
 */
function calculatePriorityScore(task) { ... }
```

## Error Handling

### Best Practices
- Always handle errors appropriately
- Use try-catch for async operations
- Provide meaningful error messages
- Log errors with sufficient context
- Don't swallow errors silently
- Use custom error types for different scenarios

**Example**:
```javascript
async function createTask(taskData) {
  try {
    const response = await fetch('/api/tasks', {
      method: 'POST',
      body: JSON.stringify(taskData)
    });
    
    if (!response.ok) {
      throw new Error(`Failed to create task: ${response.statusText}`);
    }
    
    return await response.json();
  } catch (error) {
    console.error('Error creating task:', error);
    throw error; // Re-throw for caller to handle
  }
}
```

## Performance Considerations

### Guidelines
- Optimize only when necessary (measure first)
- Avoid premature optimization
- Use appropriate data structures
- Minimize DOM manipulations
- Debounce/throttle expensive operations
- Lazy load when appropriate
- Cache expensive computations when beneficial

### When to Optimize
- Measured performance issues exist
- User experience is impacted
- Resource constraints are identified
- Profiling shows specific bottlenecks

## Summary

### Key Takeaways
1. **DRY**: Don't repeat yourself - extract common logic
2. **SOLID**: Follow solid design principles for maintainability
3. **Lint**: Use linting tools to enforce consistency
4. **Format**: Use Prettier for consistent formatting
5. **Simple**: Keep code simple and readable
6. **YAGNI**: Don't over-engineer solutions
7. **Test**: Write tests that validate behavior
8. **Document**: Document the "why", not the "what"

### Code Review Checklist
- [ ] Code follows DRY principle
- [ ] Functions have single responsibilities
- [ ] No linting errors or warnings
- [ ] Code is properly formatted
- [ ] Logic is simple and easy to understand
- [ ] No unnecessary abstractions or complexity
- [ ] Meaningful variable and function names
- [ ] Appropriate error handling
- [ ] Tests cover new functionality
- [ ] Documentation added where needed

## Resources

- [Clean Code by Robert C. Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
- [SOLID Principles](https://en.wikipedia.org/wiki/SOLID)
- [ESLint Documentation](https://eslint.org/)
- [Prettier Documentation](https://prettier.io/)
- [You Aren't Gonna Need It](https://martinfowler.com/bliki/Yagni.html)
- [Keep It Simple, Stupid](https://en.wikipedia.org/wiki/KISS_principle)
