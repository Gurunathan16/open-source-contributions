<details>
<summary><strong> 
  Spring Security - Documentation Update 
  
</strong></summary>

- **Project**: [spring-projects/spring-security](https://github.com/spring-projects/spring-security)
- **Type of Contribution**: Documentation improvement
- **What I Did**: Added a note recommending overriding `equals()` and `hashCode()` in custom `UserDetails` implementations for maintaining session.
- **PR Link**: [#17141](https://github.com/spring-projects/spring-security/pull/17141)
- **Merged In**: `6.3.x`, `6.4.x`, `6.5.x`, and `main` branches
- **Maintainer Feedback**: “Thanks, @Gurunathan16! This is now merged into 6.3.x, 6.4.x, 6.5.x, and main” — [jzheaux]

### 🔐 Understanding the Importance of Overriding `equals()` and `hashCode()` in Custom `UserDetails`

#### 🧠 What's the issue?

In Spring Security, when a user logs in, their session is tracked. This tracking is important for features like:

- Limiting the number of concurrent sessions
- Invalidating sessions after logout
- Checking currently active users

Spring manages this via a component called `SessionRegistryImpl`.

#### 🔍 What does SessionRegistryImpl do?

`SessionRegistryImpl` tracks user sessions using this structure internally:

```java
ConcurrentMap<Object, Set<String>>
```

Here:
- The key is the user principal (typically your UserDetails object).

- The value is a set of sessionId's associated with that user.

#### 🔄 So what’s the problem?  

If you create your own custom class for UserDetails (which many people do to add extra fields like name, age, role, etc.), then Spring will store your object as the key in that map.

Now here's the catch:

Java collections (like Map) use equals() and hashCode() to determine if two keys are the same.

If your custom UserDetails class does not override equals() and hashCode(), then:

- The session tracker won't be able to match a user to their existing session(s).
- You may run into strange bugs like:
  - The same user being logged in twice,
  - Logout not working as expected,
  - Session limiting (like max 1 session per user) failing.

#### 📌 Why does this contribution matter?  

This documentation note alerts developers to this subtle but important requirement:

> If you are using a custom implementation of `UserDetails`, ensure you override the **equals()** and **hashCode()** methods.
The default `SessionRegistry` implementation in Spring Security relies on an in-memory Map that uses these methods to correctly identify and manage user sessions.
Failing to override them may lead to issues where session tracking and user comparison behave unexpectedly.

This helps:

- Prevent bugs that are hard to detect,
- Ensure expected security features work reliably,
- Save time for other developers facing mysterious session issues.
</details>

---
