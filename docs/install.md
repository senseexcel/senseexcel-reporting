# Install

Follow next steps to get your SER ruinning

## Files

## Virtual Proxy

## Analytics Connection

## Security Rules

### License Rule

Go to QMC Add a Security Rule select unspecified from Template.

| Property         | Value                |
|------------------|--------------------  |
| Name             | SER License          |
| Resource filter: | License_*            |
| Condition:       | !user.IsAnonymous()  |

<img src="https://github.com/senseexcel/senseexcel-reporting/raw/master/docs/qmc-securityrule-license.PNG" width="250">


### Scheduler Rule
