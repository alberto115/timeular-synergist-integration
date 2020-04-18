# Integration flow

## Brainstorming:

A lambda function that will run every hour and create new input activities not in progress into Synergist.


### Format

#### Activities

Activities in Timeular will match the client plus activity on synergist.

Name of activities must follow pattern:
* client.activity: name of client

We will only use the first part before the first colon to link both platforms. The second part it just to be make it more human on Timeular.

#### Description

Description can have any format. The description must be added as it is a requirement (in our case at least) on Synergist, and it won't create the entry if nothing input.

There isn't any specific requirement for the messages.

#### Times

The time is converted automatically from Timeular to Synergist.


### API Time entries

**Mandatory parameters**
* stoppedAfter (2017-01-01T09:30:00.000)
* startedBefore (2017-01-01T13:00:00.000)

**Response**
```json
{
  "timeEntries": [
    {
      "id": "987",
      "activity": {
        "id": "123",
        "name": "1234.123: Company development",
        "color": "#a1b2c3",
        "integration": "zei"
      },
      "duration": {
        "startedAt": "2017-01-01T09:30:00.000",
        "stoppedAt": "2017-01-01T13:00:00.000"
      },
      "note": {
        "text": "API integration",
        "tags": [
          {
            "indices": [
              0,
              11
            ],
            "key": "development"
          }
        ],
        "mentions": [
          {
            "indices": [
              25,
              29
            ],
            "key": "John"
          }
        ]
      }
    }
  ]
}
```

**Conversion to Synergist**

_Fields mapping_
* _Job:_ timeEntries.activity.name (first part before the first colon)
* _Date:_ timeEntries.duration.startedAt (day when it started) (Multiple days not supported on v.1)
* _Normal Hours:_ timeEntries.duration.stoppedAt - timeEntries.duration.startedAt (converted to hours)
* _Description:_ timeEntries.note.text

_Example data_
* _Job:_ 1234.123
* _Date:_ 2017-01-01
* _Normal Hours:_ 4
* _Description:_ API integration

**Request to synergist**

Synergist use Timesheets API to create new entries.

_Parameters_
* _user:_ Synergist username
* _password:_ Synergist user password
* _version:_ API version (3.4 currently)
* _action:_ create/update/delete
* _input:_ array with data parameters

_Data parameters_
* _tspJobAndPhase:_ Job.Phase
* _tspTimeDate:_ Date of the timesheet
* _tspResourceCode:_ Resource code (Filled automatically on UI)
* _tspHoursNormal:_ Time spent (Not in documentation)
* _tspWorkDone:_ Work done description (Not in documentation)

_Example request_
```
http://<SERVER>/jsonapi/tspending.json?user=user&password=user&version=3.4&action=create&input=
{
  "data": [
    {
      "tspJobAndPhase": "1234.123",
      "tspTimeDate": "2017-01-01",
      "tspResourceCode": "3/U",
      "tspHoursNormal": "4",
      "tspWorkDone": "API integration"
    }
  ]
}
```


### API Activities
Activities synchronisation will not be in place in this version.
