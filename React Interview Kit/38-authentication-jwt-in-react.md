---
title: "Authentication & JWT in React"
source: https://www.geekyfrontend.in/learn/react-interview-kit/authentication-jwt-in-react
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Authentication & JWT in React

*3 min read · Updated 24 Mar 2026 Topic 38 of 62 Bookmark Mark done 8 · Topic 38 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/authentication-jwt-in-react](https://www.geekyfrontend.in/learn/react-interview-kit/authentication-jwt-in-react)

---

# 8.3 - Authentication & JWT in React

---

## 🎯 What Interviewers Actually Ask

| Question | Level | Frequency |
| --- | --- | --- |
| "How do you handle authentication in React?" | 🟡🔴 | ★★★★★ |
| "What is JWT?" | 🟡 | ★★★★☆ |
| "Where do you store tokens?" | 🟡🔴 | ★★★★☆ |
| "How do you protect API routes on the client?" | 🟡 | ★★★☆☆ |

---

## The JWT Authentication Flow

1. User submits login credentials (email + password)
2. Server validates credentials and returns a **JWT** (JSON Web Token)
3. Client stores the token
4. Client sends the token with every API request via the `Authorization: Bearer <token>` header
5. Server validates the token on each request and returns data or 401

### Login implementation

jsx

jsx

```
async function login(email, password) {
  const response = await fetch('/api/auth/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password }),
  });

  if (!response.ok) {
    const error = await response.json();
    throw new Error(error.message || 'Login failed');
  }

  const { token, user } = await response.json();
  localStorage.setItem('token', token);
  return user;
}
```

### Authenticated API calls

jsx

jsx

```
async function fetchWithAuth(url, options = {}) {
  const token = localStorage.getItem('token');

  const response = await fetch(url, {
    ...options,
    headers: {
      ...options.headers,
      'Content-Type': 'application/json',
      Authorization: token ? `Bearer ${token}` : '',
    },
  });

  if (response.status === 401) {
    localStorage.removeItem('token');
    window.location.href = '/login';
    throw new Error('Session expired');
  }

  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.json();
}
```

### AuthContext pattern

jsx

jsx

```
const AuthContext = createContext(null);

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const token = localStorage.getItem('token');
    if (token) {
      fetch('/api/auth/me', {
        headers: { Authorization: `Bearer ${token}` },
      })
        .then(r => r.ok ? r.json() : Promise.reject())
        .then(setUser)
        .catch(() => localStorage.removeItem('token'))
        .finally(() => setLoading(false));
    } else {
      setLoading(false);
    }
  }, []);

  const login = async (email, password) => {
    const res = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password }),
    });
    const { token, user } = await res.json();
    localStorage.setItem('token', token);
    setUser(user);
  };

  const logout = () => {
    localStorage.removeItem('token');
    setUser(null);
  };

  if (loading) return <FullPageSpinner />;

  return (
    <AuthContext.Provider value={{ user, login, logout, isAuthenticated: !!user }}>
      {children}
    </AuthContext.Provider>
  );
}

function useAuth() {
  const context = useContext(AuthContext);
  if (!context) throw new Error('useAuth must be used inside AuthProvider');
  return context;
}
```

### Token storage comparison

|  | localStorage | httpOnly cookie |
| --- | --- | --- |
| JS access | Yes (vulnerable to XSS) | No (more secure) |
| Sent with requests | Manual header | Automatic with same-domain requests |
| XSS vulnerability | High | Low |
| CSRF vulnerability | Low | High (mitigate with CSRF tokens) |
| Setup complexity | Simple | Requires backend cooperation |

For interviews, localStorage is the standard answer. Mention httpOnly cookies as the more secure production option.

---

## ✅ Key Takeaways

- JWT flow: login, receive token, store it, attach to every request, handle 401.
- Use **AuthContext + useAuth hook** for app-wide auth state.
- **localStorage** for simplicity. **httpOnly cookies** for production security.
- Always handle **token expiration** (401 responses) with redirect to login.
- Show a **loading spinner** while validating the token on initial load.
- Client-side auth is **UX only**. Server must validate tokens independently.

---

## Learn More

- [Introduction to JWT jwt.io](https://jwt.io/introduction)
- [React Router — home & docs reactrouter.com](https://reactrouter.com)
- [JWT auth: access & refresh tokens YouTube](https://www.youtube.com/watch?v=AcYF18oGn6Y)
- [JWT authentication (React + Node) YouTube](https://www.youtube.com/watch?v=KgXT63wPMPc)
- [Storing a JWT in a cookie YouTube](https://www.youtube.com/watch?v=Avfa7RrPx_Q)
