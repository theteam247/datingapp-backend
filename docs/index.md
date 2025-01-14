# How to Integrate the Membership Invitation Flow into Your Web Application

This guide provides a step-by-step approach to integrate the membership invitation flow of UserHub into your web application. The focus will be on leveraging the **User API** and **Admin API** for authentication and sending invitations programmatically. This guide is tailored for developers who prefer building their own UI rather than redirecting users to the UserHub hosted portal.

---

## Prerequisites

Before you begin, ensure you have:

1. A UserHub account and access to the developer portal.
2. A development tenant set up in UserHub. Sign up [here](https://admin.userhub.com/signup) and follow the [onboarding guide](https://userhub.com/docs/getting-started) to step 5. [image placeholder: Screenshot of onboarding step 5.]
3. Familiarity with UserHub's APIs:
   - [User API](https://userhub.com/docs/userapi)
   - [Admin API](https://userhub.com/docs/adminapi)
4. Basic knowledge of building web applications with backend authentication.

---

## Step 1: Understand the Invitation Flow

UserHub supports two methods for sending membership invitations:

1. **Using the Admin API:** Mimics the behavior of sending invitations from the admin console. Best suited for applications with backend authentication.
2. **Using the User API:** Mimics sending invitations from the UserHub hosted portal. Ideal for client-side authentication workflows.

[image placeholder: Diagram showing Admin API and User API workflows.]

We will focus on integrating the **User API** invitation flow in a web application.

---

## Step 2: Authenticate the User

Before sending invitations, your application must authenticate the user to obtain a valid token. Depending on your application’s architecture, use one of the following methods:

### **A. Admin API Authentication**

This method requires backend support to securely create an API session.

1. Call the [Create API Session endpoint](https://userhub.com/docs/adminapi/users/create-api-session) with admin credentials to authenticate the user.

   **Example Request:**

   ```http
   POST https://api.userhub.com/adminapi/users/create-api-session
   Content-Type: application/json

   {
       "username": "admin@example.com",
       "password": "your_admin_password"
   }
   ```

   [image placeholder: Screenshot of API session creation request in Postman.]

2. The response will include a session token. Use this token to authenticate subsequent API requests.

**Example Response:**

```json
{
    "sessionToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### **B. Token Exchange Authentication**

This method is suitable for applications without backend authentication.

1. Obtain an initial token (e.g., OAuth token) for the user.

2. Exchange the token using the [Token Exchange endpoint](https://userhub.com/docs/userapi/session/exchange-token).

   **Example Request:**

   ```http
   POST https://api.userhub.com/userapi/session/exchange-token
   Content-Type: application/json

   {
       "providerToken": "initial_oauth_token",
       "provider": "google"
   }
   ```

   [image placeholder: Screenshot of token exchange request in Postman.]

3. The response will include a session token for authenticating API requests.

**Example Response:**

```json
{
    "sessionToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600,
    "tokenType": "Bearer"
}
```

---

## Step 3: Integrate the Invitation Flow

Once authenticated, use the User API to send invitations.

### **Endpoint:** [Create Join Organization](https://userhub.com/docs/userapi/flows/create-join-organization)

1. Construct the API request to invite a user:

   **Example Request:**

   ```http
   POST https://api.userhub.com/userapi/flows/create-join-organization
   Authorization: Bearer {sessionToken}
   Content-Type: application/json

   {
       "email": "invitee@example.com",
       "role": "member"
   }
   ```

   [image placeholder: Screenshot of sending invitation request in Postman.]

2. Handle the API response:

   **Example Response:**

   ```json
   {
       "status": "success",
       "message": "Invitation sent successfully."
   }
   ```

   [image placeholder: Screenshot of successful invitation response.]

### **Error Handling:**

Ensure your application gracefully handles errors such as invalid tokens, missing parameters, or unauthorized access. Example error response:

```json
{
    "error": "Invalid session token. Please authenticate again."
}
```

[image placeholder: Example of error response shown in application UI.]

---

## Step 4: Build the Invitation Modal UI

1. Design a modal form to collect the invitee’s email and role.


2. On submission, trigger the API request to send the invitation.

**Example Workflow:**

1. User fills out the modal form.
2. Form submission triggers an API call to the `Create Join Organization` endpoint.
3. Display success or error messages based on the API response.

### **Example Code Snippet (Frontend):**

```javascript
async function sendInvitation(email, role, sessionToken) {
    try {
        const response = await fetch(
            'https://api.userhub.com/userapi/flows/create-join-organization',
            {
                method: 'POST',
                headers: {
                    'Authorization': `Bearer ${sessionToken}`,
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ email, role }),
            }
        );

        const result = await response.json();
        if (response.ok) {
            alert('Invitation sent successfully!');
        } else {
            alert(`Error: ${result.error}`);
        }
    } catch (error) {
        console.error('Error sending invitation:', error);
        alert('An unexpected error occurred. Please try again.');
    }
}
```

[image placeholder: Screenshot of modal form UI for sending invitations.]

---

## Step 5: Test and Verify

1. Create test accounts to simulate the invitation flow.
2. Verify successful invitations in the UserHub admin portal. [image placeholder: Screenshot of UserHub admin portal showing pending invitations.]
3. Debug and handle edge cases, such as duplicate invitations or invalid emails.

---

## Conclusion

Integrating the membership invitation flow into your web application provides a seamless user experience, empowering developers to fully customize their application’s UI. Follow the steps outlined in this guide to authenticate users, send invitations, and handle responses effectively.

For additional information, refer to the [User API Documentation](https://userhub.com/docs/userapi).

