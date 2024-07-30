---
design_pattern_id: 10
name: Confirm or Cancel a Servicenow Workplace reservation
description: Uses the Servicenow workplace reservation API to keep reservations accountable
systems: [servicenow]
purple_chat_link: https://developer.moveworks.com/creator-studio/developer-tools/purple-chat-builder/?workspace={"title"%3A"My+Workspace"%2C"botSettings"%3A{"name"%3A"Lumi"}%2C"mocks"%3A[{"id"%3A5690%2C"title"%3A"Mock+1"%2C"transcript"%3A{"settings"%3A{"colorStyle"%3A"LIGHT"%2C"startTime"%3A"11%3A43+AM"%2C"defaultPerson"%3A"GWEN"%2C"editable"%3Atrue%2C"botName"%3A"Lumi"}%2C"messages"%3A[{"from"%3A"BOT"%2C"text"%3A"Hello+Gwen%2C\n+You+have+a+desk+reserved+in+the+office+at+9+am+today.+Could+you+confirm+that+you+will+be+making+that+reservation%3F"%2C"cards"%3A[{"title"%3A"Reservation+Confirmation"%2C"text"%3A"Please+Confirm+your+reservation."}%2C{"buttons"%3A[{"style"%3A"PRIMARY"%2C"text"%3A"Yes%2C+I+will+be+there"}%2C{"text"%3A"No%2C+I+cannot+make+it"}]}]}%2C{"from"%3A"ANNOTATION"%2C"text"%3A"Lumi+awaits+user+confirmation+to+either+hold+reservation%2C+or+reach+out+to+Servicenow+to+cancel"}%2C{"from"%3A"USER"%2C"text"%3A"No%2C+I+cannot+Make+it"}%2C{"from"%3A"BOT"%2C"text"%3A"Confirmed%2C+cancelling+your+desk+reservation+at+the+office+today"}%2C{"from"%3A"ANNOTATION"%2C"text"%3A"If+the+response+is+positive+no+action+is+taken+and+the+reservation+is+held.\n+If+the+response+is+negative+<p>Lumi+reaches+out+to+the+<code>%2Fapi%2Fsn_wsd_rsv%2Freservation%2Fcancel<%2Fcode><%2Fp>+Servicenow+API+endpoint+and+cancels+the+reservation+automatically."}]}}]}
time_in_minutes: 45
difficulty_level: Advanced
---

# **Introduction**

In the modern business environment, ensuring that office asset reservations that are no longer necessary get opened back up is crucial for maintaining workflow efficiency and employee satisfaction. However, in the bustling day-to-day activities, reservation cancellations can often be forgotten or delayed, causing bottlenecks in operations. Addressing this challenge, our use case tutorial introduces an innovative solution leveraging Service alongside Moveworks Events triggered paths to automate approval reminders. By setting up reminder with a buffer before a reservation, users are prompted about their outstanding reservations, thus enhancing overall productivity.

This tutorial will walk you through leveraging the powerful capabilities of ServiceNow's Flow Designer to implement this solution seamlessly within your organization. Whether you're looking to minimize delays in reservation cancellations or ensure that unanticipated schedule changes minimize disruption, this guide provides step-by-step instructions to enhance your workflow management through effective reminder automation.

# What are we building?

## Conversation Design

[This purple chat](https://developer.moveworks.com/creator-studio/developer-tools/purple-chat-builder/?workspace={"title"%3A"My+Workspace"%2C"botSettings"%3A{"name"%3A"Lumi"}%2C"mocks"%3A[{"id"%3A5690%2C"title"%3A"Mock+1"%2C"transcript"%3A{"settings"%3A{"colorStyle"%3A"LIGHT"%2C"startTime"%3A"11%3A43+AM"%2C"defaultPerson"%3A"GWEN"%2C"editable"%3Atrue%2C"botName"%3A"Lumi"}%2C"messages"%3A[{"from"%3A"BOT"%2C"text"%3A"Hello+Gwen%2C\n+You+have+a+desk+reserved+in+the+office+at+9+am+today.+Could+you+confirm+that+you+will+be+making+that+reservation%3F"%2C"cards"%3A[{"title"%3A"Reservation+Confirmation"%2C"text"%3A"Please+Confirm+your+reservation."}%2C{"buttons"%3A[{"style"%3A"PRIMARY"%2C"text"%3A"Yes%2C+I+will+be+there"}%2C{"text"%3A"No%2C+I+cannot+make+it"}]}]}%2C{"from"%3A"ANNOTATION"%2C"text"%3A"Lumi+awaits+user+confirmation+to+either+hold+reservation%2C+or+reach+out+to+Servicenow+to+cancel"}%2C{"from"%3A"USER"%2C"text"%3A"No%2C+I+cannot+Make+it"}%2C{"from"%3A"BOT"%2C"text"%3A"Confirmed%2C+cancelling+your+desk+reservation+at+the+office+today"}%2C{"from"%3A"ANNOTATION"%2C"text"%3A"If+the+response+is+positive+no+action+is+taken+and+the+reservation+is+held.\n+If+the+response+is+negative+<p>Lumi+reaches+out+to+the+<code>%2Fapi%2Fsn_wsd_rsv%2Freservation%2Fcancel<%2Fcode><%2Fp>+Servicenow+API+endpoint+and+cancels+the+reservation+automatically."}]}}]}) shows the experience we are going to build.

**This use case is made up of:**

✨ **Triggers:** Servicenow Event flow

🤲 **Slots**: Reservation

🏃‍♂️ **Actions:** Cancel reservation if necessary

📜 **Response Data:** Confirm user action

📚 **Guidelines:** Select fields to render from RaaS report

Based on the needs of this use case, we should build an [Event Triggered Path](https://developer.moveworks.com/creator-studio/resources/design-pattern?id=static-query)

## API Design

There’s two APIs needed to build this use case.

### API #1: The Moveworks Events API

1. [Moveworks API](https://developer.moveworks.com/openapi/reference/#operation/sendMessageForEvent)**:** An API to send proactive events to a Moveworks user

- Authenticating to the Moveworks API (follow [our guide](https://developer.moveworks.com/openapi/reference/#operation/testAuth))
  - Authenticating is relatively easy - you can obtain your Bearer Token from you Moveworks Customer Success Team
- [Install Postman](https://www.postman.com/downloads/)

### API #2: The ServiceNow Reservation API

Since we want to get a RaaS report, we only need to reference the report in our API call and get the results from the report to display within Creator Studio.

# **Prerequisites**

- Admin access to Servicenow

# **Walkthrough**

## **Step 1: Create Event**

1. Follow [steps to create a new path and api key](https://developer.moveworks.com/creator-studio/quickstart/events/) in Creator studio
2. Review <https://developer.moveworks.com/creator-studio/quickstart/event-triggered-paths/#setting-up-the-path-slot> to ensure your event captures the
    1. Name the slot `Reservation`

## **Step 2: Build in Creator Studio**

1. Add a follow up action to your existing event
2. Add a question to prompt confirming the reservation
    1. “Please confirm your reservation”
3. In the affirmative path, no changes are necessary. End the path with an acknowledgement
4. In the negative path make a call to the [Servicenow reservation cancellation endpoint](https://docs.servicenow.com/bundle/washingtondc-api-reference/page/integrate/inbound-rest/concept/wsd_reservation-api.html#title_wsd_reserv-PATCH-cancel)
    1. **PATCH /api/sn_wsd_rsv/reservation/cancel/{sys_id}**

        <https://docs.servicenow.com/bundle/washingtondc-api-reference/page/integrate/inbound-rest/concept/wsd_reservation-api.html#title_wsd_reserv-PATCH-cancel>

5. Create an External API call
    1. Use existing Servicenow connector
    2. Add `/api/sn_wsd_rsv/reservation/cancel/{{Reservartion}}` in the endpoint path section
        1. Ensure your spelling of reservation matches the spelling you used setting up the event slot
    3. Set method to `PATCH`

## Step 3: See it in action

An event will be sent to the user once the job is triggered!

# Congratulations

You have successfully navigated through the intricacies of setting up automated reservation reminders within ServiceNow. By leveraging the powerful combination of ServiceNow Flow Designer and the Moveworks Event triggered Paths API, you've taken a significant step towards optimizing workflow efficiency and ensuring that approval requests are completed in a timely manner.

This tutorial guided you through each pivotal stage - from the trigger initiation upon an approval record's creation, employing conditional logic to ascertain the need for reminders, to the pivotal dispatch of messages to users reminding them of their pending requests. Your ability to integrate these components demonstrates not only technical proficiency but also a commitment to enhancing operational processes within your organization.

We hope this tutorial has empowered you with the knowledge and confidence to implement automated approval reminders effectively. This isn't just a step forward in streamlining your approval processes; it's a leap towards maximizing productivity, reducing bottlenecks, and improving overall satisfaction across your teams.

Congratulations once again on completing this tutorial. Your efforts today lay the groundwork for more efficient, responsive, and user-friendly workflow management. Here's to many more successful implementations in your journey towards employee optimization!
