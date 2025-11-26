# Instructor Login Debugging Guide

## Issue
Instructor login not working or courses not showing

## Backend Status
✅ Backend is working correctly
✅ Login API returns valid token
✅ Courses API returns 2 courses for john@edumaster.com
✅ Database has correct data

## Steps to Fix

### 1. Clear Browser Cache and Storage
1. Open browser DevTools (F12)
2. Go to Application tab (Chrome) or Storage tab (Firefox)
3. Click "Clear site data" or manually delete:
   - Local Storage (remove 'token' and 'user')
   - Session Storage
   - Cookies
4. Refresh the page (Ctrl + Shift + R for hard refresh)

### 2. Test Login Flow
1. Go to http://localhost:5174/login
2. Click the "Instructor" quick login button
3. Watch the browser console for any errors
4. Check Network tab to see if API calls are successful

### 3. Verify Token Storage
After logging in:
1. Open DevTools → Application → Local Storage
2. Check if 'token' exists
3. Check if 'user' exists and has role: "INSTRUCTOR"

### 4. Check Console for Errors
1. Open DevTools → Console tab
2. Look for any red error messages
3. Common issues:
   - CORS errors
   - 401 Unauthorized
   - Network errors
   - JavaScript errors

### 5. Verify API Calls
1. Open DevTools → Network tab
2. Filter by "Fetch/XHR"
3. After login, you should see:
   - POST /api/auth/login (Status: 200)
   - GET /api/instructor/my-courses (Status: 200)
4. Click on each request to see response data

### 6. Manual Login Test
If quick login doesn't work, try manual login:
- Email: john@edumaster.com
- Password: password123

### 7. Check Role in User Object
After login, in console type:
```javascript
JSON.parse(localStorage.getItem('user'))
```
Should show: `{ role: "INSTRUCTOR", ... }`

### 8. Force Logout and Re-login
1. Click logout (if visible)
2. Or manually run in console:
```javascript
localStorage.clear()
window.location.href = '/login'
```
3. Login again

## Expected Behavior

After successful login as instructor:
1. Redirected to /instructor (dashboard)
2. Dashboard shows:
   - Courses Created: 2
   - Total Students: (varies)
   - Charts and analytics
3. Navigate to "My Courses" should show:
   - Modern React with Redux
   - Node.js & Express Backend Bootcamp

## If Still Not Working

Check these in browser console:
```javascript
// 1. Check if user is logged in
console.log('User:', JSON.parse(localStorage.getItem('user')))

// 2. Check token
console.log('Token:', localStorage.getItem('token'))

// 3. Manually fetch courses
fetch('http://localhost:5000/api/instructor/my-courses', {
  headers: { 
    'Authorization': 'Bearer ' + localStorage.getItem('token'),
    'Content-Type': 'application/json'
  }
})
.then(r => r.json())
.then(data => console.log('Courses:', data))
.catch(err => console.error('Error:', err))
```

## Contact
If none of these work, check:
- Is backend running on port 5000?
- Is frontend running on port 5174?
- Any firewall blocking localhost connections?
