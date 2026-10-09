# Leave Request Workflow with Camunda 8

A small Leave Request process in Camunda 8. An employee's request enters the process, a manager approves it through a user task, and the process ends. I started the process by calling the Camunda REST API from Postman rather than from the UI.

## Setup

- Camunda 8 Run (local cluster on `localhost:8080`)
- Camunda Modeler for the BPMN model
- Postman for the API call
- Operate for checking the running instance

## Steps I followed

**1. Modeled the process.** In Camunda Modeler I drew a Start Event, a User Task named "Approve Leave", and an End Event, and set the Process ID to `leave-request`.

![BPMN diagram](images/bpmn-diagram.png)

**2. Deployed it.** I deployed the diagram from the Modeler to my local cluster.

**3. Started an instance through the API.** In Postman I sent a `POST` request to `http://localhost:8080/v2/process-instances` with the header `Content-Type: application/json` and this body:

```json
{
  "processDefinitionId": "leave-request",
  "variables": {
    "employeeName": "Ravi",
    "days": 3
  }
}
```

The response was `200 OK`, and the `processInstanceKey` was: **`<paste your key here>`**

![Postman request and response](images/postman.png)

**4. Verified in Operate.** The instance is active and waiting at "Approve Leave", and both variables (`employeeName`, `days`) are visible.

![Operate running instance](images/operate.png)

## Viva answers

1. **processDefinitionId vs processInstanceKey:** the definition ID is the name of the process model (`leave-request`) and is the same for every run. The instance key is a unique number for one particular run.
2. **Wrong Process ID:** the API returns `404 Not Found` because no matching process definition exists, and no instance is created.
3. **Starting the process 3 times:** Operate shows 3 separate active instances, each waiting at "Approve Leave" with its own key.

