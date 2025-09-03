# Types of Testing in Ruby on Rails with RSpec

Welcome to the magical world of testing! In this lesson, we’ll explore the three main types of tests you’ll write as a Rails developer: **unit**, **integration**, and **end-to-end (E2E)** tests. We’ll cover what they are, when to write them, how much to write, and show you some snazzy examples. By the end, you’ll be able to impress your friends, your instructors, and maybe even your future self.

---

## Why Do We Test?

Testing is like having a safety net for your code.

- Without tests, even small changes can accidentally break features in other parts of your app.
- With tests, you gain confidence to refactor, ship new features faster, and catch bugs before your users do.
- Tests are also documentation—future developers (including future you!) can see how your code is supposed to work.

Each type of test helps you catch different kinds of problems, from math mistakes in your models to broken user flows in your UI. Let’s see how!

---

## Rails RSpec Test Types Mapping

| Test Type                        | RSpec Metadata Symbol |
|----------------------------------|----------------------|
| Unit (Models, Classes)           | `:model`             |
| Integration (API/Controllers)    | `:request`           |
| End-to-End (User flows/features) | `:feature`           |

---

## What Are the Types of Tests?

Testing is like eating your vegetables: you might not always want to, but you’ll be glad you did. In Rails (and most web apps), we break tests into three main categories:

- **Unit Tests**: Test the smallest pieces of code (usually methods or classes) in isolation.
- **Integration Tests**: Test how different parts of your app work together (e.g., models + controllers).
- **End-to-End (E2E) Tests**: Test the whole app from the user’s perspective, simulating real user actions.

### Integration vs. End-to-End: What’s the Difference?

Many beginners confuse integration and E2E tests since both involve multiple parts of the system. Here’s a quick comparison:

- **Integration**: Tests pieces working together, often at the API or controller level. Think of integration tests as “checking the plumbing.”
- **E2E**: Simulates a real user interacting with a browser. E2E tests are like “turning on the faucet and seeing water flow.”

Let’s break these down!

---

## Unit Tests

### What are Unit Tests?

Unit tests focus on the smallest units of code—think of them as the “microscope” of testing. In Rails, this usually means testing individual models, methods, or classes.

Unit tests should not hit the database or external APIs unless absolutely necessary. Keep them fast and focused by stubbing or mocking anything outside the class you’re testing.

#### Why are Unit Tests Important?

Unit tests are like checking each LEGO brick before you build a castle.

- They run very fast because they don’t need to load the full Rails environment or a browser.
- They should test only the code in that class or method, nothing else.
- If you notice a unit test hitting the database, consider stubbing or mocking instead.

Unit tests prevent bugs in your business logic—like payment calculations, user name formatting, or anything else that could go wrong in a single method or class.

### When should I write Unit Tests?

- When you want to make sure a method or class does exactly what it’s supposed to do.
- When you’re writing business logic (e.g., calculations, validations).

### How many Unit Tests should I write?

Write unit tests for any code that contains logic or could break. If it’s important, test it!

**Example (Model Spec):**

```ruby
# spec/models/user_spec.rb
RSpec.describe User, type: :model do
  describe '#full_name' do
    let(:user) { User.new(first_name: 'Ada', last_name: 'Lovelace') }

    it 'returns the first and last name concatenated' do
      expect(user.full_name).to eq('Ada Lovelace')
    end
  end
end
```

---

## Integration Tests

### What are Integration Tests?

Integration tests check that different parts of your app play nicely together. They’re like the “group project” of testing: do your models, controllers, and views work together as expected?

Integration tests should cover how multiple parts of your backend interact (models, controllers, mailers, etc.), but don’t need to simulate a real browser or user clicking around. They may hit the database, but shouldn’t depend on the UI.

#### Why are Integration Tests Important?

Integration tests catch mismatches between APIs, controllers, and models—like when your controller expects a parameter your model doesn’t handle, or when two parts of your app disagree about what a user is allowed to do.

### When should I write Integration Tests?

- When you want to test a workflow or process that involves multiple components (e.g., creating a user via a form).
- When you want to catch bugs that only appear when parts of your app interact.

### How many Integration Tests should I write?

Write integration tests for your app’s most important workflows—especially those that are critical to your users.

**Example (Request Spec):**

```ruby
# spec/requests/users_spec.rb
RSpec.describe 'Users', type: :request do
  describe 'POST /users' do
    it 'creates a new user and redirects to the user page' do
      post '/users', params: { user: { first_name: 'Ada', last_name: 'Lovelace' } }
      user = User.last
      expect(response).to redirect_to(user_path(user))
      follow_redirect!
      expect(response.body).to include('Ada Lovelace')
    end
  end
end
```

---

## End-to-End (E2E) Tests

### What are E2E Tests?

E2E tests simulate a real user interacting with your app—clicking buttons, filling out forms, and generally living their best digital life. In Rails, these are often called **feature specs** and use tools like Capybara.

E2E tests should not care about internal implementation details. They only care about what the user can see and do, not which controller method was called or what the database schema looks like.

#### Why are E2E Tests Important?

E2E tests ensure the whole signup or checkout process actually works, from the user’s perspective. They catch issues that only show up when everything is wired together—like a missing button or a broken redirect.

### Why are E2E Tests Slow?

E2E tests spin up a browser, load the full Rails app, run through the entire stack, and wait for UI elements to appear. Because of this, they run much slower than unit or integration tests. That’s why we keep them focused on just the most important user flows.

### When should I write E2E Tests?

- When you want to make sure the whole system works from the user’s perspective.
- For your app’s most important user journeys (e.g., signing up, making a purchase).

### How many E2E Tests should I write?

Write E2E tests for your app’s most critical paths. Don’t try to test every possible interaction—focus on what matters most.

**Example (Feature Spec with Capybara):**

```ruby
# spec/features/user_signs_up_spec.rb
require 'rails_helper'

RSpec.feature 'User signs up', type: :feature do
 scenario 'with valid details' do
  visit '/signup'
  fill_in 'First name', with: 'Ada'
  fill_in 'Last name', with: 'Lovelace'
  fill_in 'Email', with: 'ada@example.com'
  fill_in 'Password', with: 'password123'
  click_button 'Sign Up'

  expect(page).to have_content('Welcome, Ada!')
 end
end
```

---

## How Much of Each Type Should I Write?

There’s no perfect formula, but here’s a handy rule of thumb (sometimes called the “testing pyramid”):

- **Lots of unit tests**: They’re fast, reliable, and easy to write.
- **Some integration tests**: Focus on key workflows.
- **A few E2E tests**: They’re slower and more brittle, so save them for the most important user journeys.

```bash
      [E2E Tests]    (~10%)
    [Integration]   (~20%)
  [Unit Tests]      (~70%)
```

Or check out [this graphic](https://martinfowler.com/bliki/TestPyramid.html) for a fancier version.

If you invert the pyramid (write too many E2E tests and not enough unit tests), your test suite will be slow, fragile, and hard to maintain. Keep the base wide and the top narrow!

If you’re ever in doubt, remember: it’s better to have some tests than none. And if you’re ever *really* in doubt, ask your instructor (or your rubber duck).

### Quick Checklist: What Test Should I Write?

Ask yourself:

1. Does this involve only one class or method? → Write a unit test.
2. Does it involve multiple parts of the app working together, but not the UI? → Write an integration test.
3. Does it mimic what a real user does? → Write an E2E test.

---

## Common Pitfalls

Even the best of us fall into these traps sometimes. Here’s what to watch out for:

- **Writing too many slow E2E tests:** They’re powerful, but if you have hundreds, your test suite will crawl.
- **Skipping unit tests and relying only on manual testing:** Manual testing is great, but it’s not a substitute for automated checks.
- **Testing Rails internals instead of your own logic:** Focus on your app’s behavior, not whether Rails itself works.
- **Not updating tests when requirements change:** Outdated tests are worse than no tests!

Avoid these, and you’ll be a happier, more productive developer.

---

## Putting It All Together: User Signup Example

Let’s see how all three types of tests work together for a single feature—user signup:

**Unit Test:**

- The `User` model validates that email is present and unique.

**Integration Test:**

- The `POST /users` endpoint creates a user, sends a welcome email, and redirects to the dashboard.

**E2E Test:**

- A user visits the signup page, fills out the form, submits it, and sees a "Welcome!" message.

Each type covers a different layer, and together they give you confidence that your app works from the inside out!

Happy testing! May your specs be green and your bugs be few.

### Summary Table

| Type         | What it tests                | When to write         | How many?         | Example file           |
|--------------|-----------------------------|----------------------|-------------------|------------------------|
| Unit         | Methods, classes, models    | For logic, calculations | Lots              | `spec/models/user_spec.rb` |
| Integration  | Workflows, multiple parts   | For key workflows     | Some              | `spec/requests/users_spec.rb` |
| End-to-End   | Full user journeys          | For critical paths    | A few             | `spec/features/user_signs_up_spec.rb` |

---

## Helpful Resources

- [Better Specs: RSpec Best Practices](http://www.betterspecs.org/)
- [Rails Testing Guide](https://guides.rubyonrails.org/testing.html)
- [Capybara Documentation](https://teamcapybara.github.io/capybara/)
- [Martin Fowler: Test Pyramid](https://martinfowler.com/bliki/TestPyramid.html)
- [Flatiron School: How to Write Tests](https://flatironschool.com/blog/how-to-write-tests/)
