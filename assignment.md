**Hosted application:** [https://cisc472-assignment6.onrender.com](https://cisc472-assignment6.onrender.com)

# Insecure Direct Object Reference (IDOR)

**Location:** `server.js`, `GET /api/students/:id`

**Trigger:** An authenticated student requests another student's ID in the URL, such as `/api/students/2`.

*Example:* Maya signs in and changes the URL to `/api/students/2`. The app returns Devon's grades because it only checks that the user is logged in.

**The Fix**: Check that the requester is a TA or that the requested ID matches the logged-in student's ID. Otherwise, return `403 Forbidden`.

**what the login response contains, and where the browser keeps it:**
After a successful login, the server returns a signed JWT in `token` and basic user information in `user`, including the username and role. The browser stores the JWT in `localStorage` under the key `marks_token`.

**which requests send that value back:**
The browser reads the token from `localStorage` and sends it in the `Authorization` header with requests to `/api/me`, `/api/students/:id`, `/api/section`, and `/api/grades`. The login request does not include a token because the user does not have one yet.

**what a student can see versus what the section view is for:**
After logging in, a student can see their own name, course, and grades. The TA's section view is for viewing the whole class, including every student's grades, the course term, and the instructor note. Because of the IDOR flaw, a student can also use `/api/students/:id` to view another student's grades.

**what is inside data/marks.json after the first request:**
The first request creates `data/marks.json` from the seed data. It contains three users, their bcrypt password hashes, six starting grade records, and the CPEG 470 section details for Fall 2026. It also contains the instructor note. Any new grades entered by the TA are added to the `grades` list.
