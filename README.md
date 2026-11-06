Entra ID Identity Governance Lab
A personal Microsoft Entra ID lab: a practice IAM help desk plus an automated access review campaign built with Power Automate.
> This is a personal lab tenant with fictional sample users, not a production environment.
Overview
This lab is a personal Microsoft Entra ID tenant where I practice the identity and access work an IAM analyst does, from access requests to access review campaigns. It runs on a self-directed tenant with sample users, not a production environment.
The lab has two parts:
A practice IAM help desk. AI-assisted tooling generates realistic identity and access tickets, and I work each one by hand in the Entra admin center.
An automated access review campaign. Three Power Automate flows send reviewer reminders, escalate overdue reviews, and record each Keep or Remove decision with a date as audit evidence.
The lab builds on three years as an Applications Analyst at OceanFirst Bank, where I handled user access requests under least-privilege principles and managed production changes through formal change control.
Architecture
![Lab architecture: tickets change the tenant, and flows review its access](images/architecture.png)
The top row is the help desk, where tickets drive changes in the tenant. The bottom rows are the review campaign, where flows read and update the review workbook and email reviewers through Outlook.com.
Tenant and accounts
The tenant has no paid Microsoft 365 licenses assigned. Each account below has one job, which keeps admin work, personal sign-ins and email separate.
Account or service	Type	Used for
Personal admin account (shows as #EXT#)	Personal Microsoft account linked into the tenant as a Member	Daily admin work in the Entra admin center
Omar Admin	Cloud account created in the tenant, Global Administrator	Power Automate and Power Platform sign-in
Personal OneDrive	Personal Microsoft account	Holds the AccessReview Excel workbook
Outlook.com lab address	Personal Microsoft account	Sends all flow emails
Gmail plus addresses	Aliases of one Gmail inbox	Stand in for reviewer inboxes

Power Platform environment	Type	Status
Default Directory	Default	Available
Omar Lab	Developer	Holds the three flows. Dataverse could not be added without a license.
Identity inventory
Nine DC Comics themed users hold 19 pieces of access across four department groups, group ownership and admin roles. Each row in the AccessReview sheet is one user and one access item.
User	Access items	Reviewer
Barbara Gordon	IT group, Authentication Administrator, Groups Administrator, Guest Inviter, Helpdesk Administrator	Victor Stone
Barry Allen	Finance group	Bruce Wayne
Bruce Wayne	Finance owner, Financial Auditors owner	Omar Singer
Cassandra Cain	HR group	Dick Grayson
Dick Grayson	HR owner, HR group	Omar Singer
Justin White	Marketing group, Marketing owner	Omar Singer
Kara Olsen	Marketing group	Justin White
Selina Kyle	Finance group, Financial Auditors owner	Bruce Wayne
Victor Stone	IT owner, IT group, User Administrator	Omar Singer
Reviewers follow two rules. Group owners review the people in their groups, and Omar Singer is the fallback reviewer for the owners at the top. Nobody reviews their own access.
Barbara Gordon holds four admin roles, the kind of stacked access a review should question. In the first campaign her Guest Inviter role was marked Remove, and Victor Stone's IT owner access was marked Keep.
![User list in the Entra admin center](images/users-list.png)
Practice help desk
Since October 9, 2026, the queue has produced 54 tickets. I have closed 48 of them: 38 resolved and 10 pushed back.
A scheduled task writes new tickets three times each weekday using the lab's real users, groups and roles. I work each ticket by hand in the Entra admin center, write work notes, and close it as Resolved or Pushed back. A debrief appears only after a ticket is closed, so I can check my approach without seeing the answer first.
Realistic details:
Two ticket types. Requests (REQ) for access and changes, Incidents (INC) for sign-in problems and suspicious activity, each with a P1 to P4 priority.
A living directory. New hires, name changes and departures from closed tickets carry into later tickets. Guest contractors arrive with an end date and get an offboarding ticket when the engagement ends.
Curveballs. About one in five tickets is a request a careful analyst should push back on, such as a reset requested on someone else's behalf or more access than the job needs. All 10 closed curveballs were caught: 9 declined, and 1 resolved with narrower access than was asked for.
Skills unlock over time. New ticket categories are switched on as I finish each part of the lab plan.
Tickets by category: group membership 12, passwords 11, MFA and sign-in 9, admin roles 7, new hires and profile changes 7, guest access 5, offboarding 3.
![The practice ticket queue](images/ticket-queue.png)
Case studies
Four tickets where the right answer was not the one the requester asked for.
Global Administrator for a week of coverage (REQ0010024)
Request. The IT Head asked to make the help desk specialist a Global Administrator while he was at a conference, so she would not be blocked on anything. He listed three tasks: manage contractor guests, update sign-in branding, and reset passwords if other admins got locked out.
What stood out. The request was legitimate and came from her manager, but Global Administrator controls the whole tenant. "We'll take it off when I'm back" is how temporary admin access becomes permanent.
What I did. Declined Global Administrator and mapped each task to a narrow role: Guest Inviter, User Administrator and Organizational Branding Administrator. Admin password resets stayed with the Global Administrator. I asked for a follow-up ticket on his return, and when it came, the temporary roles were trimmed. A later request for Privileged Authentication Administrator (REQ0010034) was declined for the same reason: that role can take over a Global Administrator's account.
![The Global Administrator ticket with work notes and debrief](images/ticket-curveball.png)
MFA fatigue (INC0010009)
Request. The Finance Head reported a burst of Authenticator prompts late at night that he had not started, and thought he might have approved one to make them stop.
What stood out. An Authenticator prompt appears only after the correct password is entered, so someone else had his password. This was a likely compromise, not a broken phone.
What I did. Verified him by phone, revoked all sessions, reset his password, reviewed his registered Authenticator devices with him for anything he did not recognize, and escalated to the security team.
Password entered on a phishing page (INC0010039)
Request. A Marketing user typed her password into a "password expiring" page from an email, realized it was fake, and reported it.
What stood out. Nothing looked wrong yet, but a stolen password plus an approved prompt is all an attacker needs.
What I did. Verified her by phone, checked the logs for activity on her account, revoked sessions, reset her password, removed a suspicious authentication method, and escalated to security.
Offboarding shortcuts (REQ0010030 and REQ0010028)
Request. On a departing analyst's last day, his manager asked for the analyst's password so he could sign in as him the next week and find missing files. Separately, he asked to rename the departing account for the replacement hire so the new person would inherit everything.
What stood out. Signing in as someone else makes the audit trail show the wrong person. Reusing an account hands over the previous owner's email, MFA and access history.
What I did. Declined both. I offered the manager direct access to the files and mailbox he needed, completed the offboarding, and created a fresh account for the new hire with only the Finance access the job required.
Configured in Entra
Everything below was set up by hand on the free tier and tested by signing in as the affected users.
Area	What I configured	How I tested it
User lifecycle	Joiner, mover and leaver steps: create with title, department, manager and usage location; change groups on a move; block sign-in, revoke sessions, remove groups and hand off ownership on a leave	Signed in as a blocked user; restored a deleted user from Deleted users; reviewed each step in the audit logs
Groups	Four department security groups, plus a Financial Auditors group, with business owners	Owners managed membership without an admin role
Admin roles	User Administrator, Helpdesk Administrator, Groups Administrator, Authentication Administrator and Guest Inviter, each assigned for a stated task	A Helpdesk Administrator could reset a standard user's password but not a User Administrator's
MFA	Microsoft Authenticator enabled in the authentication methods policy; Temporary Access Pass for users without a working method	Registered MFA as test users; removed lost devices and re-registered
Guest access	B2B invitations with company and sponsor recorded; email one-time passcode sign-in; offboarding at engagement end	Signed in as a guest through the tenant-specific URL and compared guest and member access
Enterprise apps	A custom app with Assignment required set to Yes, assigned to Finance users only	A Finance user saw the app in My Apps and a Marketing user did not
Monitoring	Audit logs and sign-in logs for changes, failures and lockouts	Triggered a real Smart Lockout and read the failure entries
Screenshots
Least-privilege admin roles assigned to the help desk specialist
![Barbara Gordon's assigned admin roles](images/barbara-roles.png)
Finance security group with its business owner
![Finance group members and owners](images/finance-group.png)
Authentication methods policy
![Microsoft Authenticator and Temporary Access Pass enabled](images/auth-methods-policy.png)
A B2B guest user
![Guest user profile showing User type Guest](images/guest-user.png)
Enterprise app with assignment required
![Expense Portal enterprise app assignment](images/app-assignment.png)
Audit log entries
![Audit log entries for admin changes](images/audit-log.png)
A Smart Lockout in the sign-in logs
![Failed sign-in showing the account lockout](images/lockout-sign-in.png)
Access review automation
Three Power Automate flows run an access review campaign from one Excel table, and all three were tested end to end on the 19-row campaign. The table lives in the AccessReview workbook on OneDrive, in a table named Review.
Column	Purpose
ReviewID	Unique key the flows use to find and update a row
User, AccessItem	Who has what
ReviewerName, ReviewerEmail	Who decides, and where the reminder goes
DueDate	When the decision is due
Decision	Pending, Keep or Remove
DecisionDate	Stamped by the flow once a decision is made
ReminderCount	How many reminders went out for that row
![The Review table in the AccessReview workbook](images/review-sheet.png)
Flow	Schedule	What it does
Access Review Reminders	Daily, kept off or weekly between tests	Reads Pending rows. Emails an escalation to the campaign owner when a review is overdue, emails a reminder to the reviewer when it is not, and adds 1 to ReminderCount.
Access Review Decisions	Daily	Finds rows with a Keep or Remove decision and no date. Stamps today's date and emails a confirmation as evidence.
Test Email	Manual	One action that confirmed the account could run flows and send email
![The Access Review Reminders flow in the Power Automate designer](images/review-flow.png)
![Run history showing successful runs](images/review-flow-runs.png)
Build choices that made the flows reliable:
Apply to each runs with concurrency set to 1, so Excel updates one row at a time and never locks.
Values from a Filter array are read with expressions such as `items('Apply_to_each')?['ReviewID']`, added through the fx button.
Dates come back in ISO 8601 format, so the overdue check compares cleanly against `utcNow()`.
Emails go out through the Outlook.com connector from a dedicated lab address.
Test results: the escalation path fired for the 3 overdue rows, reminders went to the other 14 Pending rows, and the Keep and Remove rows were stamped with a decision date.
Problems solved
Most of the build time went to account types, licensing and connector limits, not the flow logic. Each fix came from reading the error, finding the cause and changing the design.
Problem	Cause	Fix
Power Automate sign-in rejected the Gmail account	It is a personal Microsoft account, and Power Platform needs a work account in the tenant	Created a cloud account in the tenant with Global Administrator
The existing #EXT# member account also failed	#EXT# means the account still signs in through the personal Gmail identity	Kept it for Entra admin work and used the new cloud account for Power Platform
Dataverse database would not create	The tenant has no license, and the Developer Plan did not assign one	Moved the review table to Excel on OneDrive
Excel connector refused the work account	Excel Online (OneDrive) only supports personal accounts	Connected it with the personal Microsoft account that owns the workbook
Email action returned Unauthorized	Microsoft restricts the built-in Mail connector for new tenants	Sent email through the Outlook.com connector from a new lab address
Outlook connection failed with a work account error	The Office 365 Outlook connector was picked by mistake	Picked Send an email (V2) under Outlook.com instead
A new run started every minute	The Recurrence trigger was set to minutes	Turned the flow off, cancelled the runs, set it to 1 day
Runs hung for over an hour	Parallel loop iterations locked the Excel file	Set Apply to each concurrency to 1 and kept the workbook closed during runs
Excel cells showed expression text	Expressions were typed into fields as plain text	Re-added them through the fx button so they evaluate
Update a row could not find rows	Same issue in Key Value	Re-added the Key Value expression through fx
Decision flow processed all 19 rows	Placeholder values like No Change counted as decisions	Standardized Decision to Pending, Keep or Remove
Skills shown
Skill	Where it shows up
User lifecycle (joiner, mover, leaver)	Help desk tickets and the offboarding case study
Least privilege	The Global Administrator case study; roles mapped to tasks
Identity threat response	MFA fatigue and phishing case studies
Social engineering awareness	10 of 10 closed curveball tickets caught
Guest and app access	B2B guests with end dates; app assignment required
Access certification campaigns	The 19-row review campaign
Reviewer assignment and fallback reviewers	Group owners review members; Omar Singer covers owners
Audit evidence	Decision dates, confirmation emails, ticket work notes, audit logs
Power Automate	Three working flows with loops, conditions, expressions, and Excel and Outlook.com connectors
Troubleshooting	The Problems solved table
Next steps
[ ] Start the Entra ID P2 trial and add Conditional Access, Privileged Identity Management, dynamic groups and native Entra access reviews
[ ] Switch enterprise app assignment from individual users to groups once P2 is active
[ ] Change the reminder flow to send one summary email per run, built with Create HTML table
[ ] Add a weekly status report with Pending, Keep and Remove counts and a completion rate
[ ] Build a Power BI Desktop dashboard on the AccessReview workbook
[ ] Remove a user from the Entra group automatically when a reviewer picks Remove
[ ] Take the SC-900 exam
