# Icinga2 Mattermost Plugin
A plugin for Icinga2 to enable notifications to Mattermost Open Source Chat.

# Usage
Assuming you are using Icinga2, the steps are:

### 1. Install
Copy _mattermost.py_ to any Location. A good Location would be _PluginContribDir_/icinga2-mattermost/.
### 2. Create the notification command:

```
object NotificationCommand "mattermost_service" {
  import "plugin-notification-command"

  command = [ PluginContribDir + "/icinga2-mattermost/mattermost.py" ]

  arguments = {
    "--channel" = {
      "description" = "mattermost channel"
      "required" = true
      "value" = "$mattermost_channel$"
    }
    "--hostalias" = {
      "required" = true
      "value" = "$mattermost_hostalias$"
    }
    "--notificationtype" = {
      "required" = true
      "value" = "$mattermost_notificationtype$"
    }
    "--oneline" = {
      "set_if" = "$mattermost_oneline$"
    }
    "--servicedesc" = {
      "required" = true
      "value" = "$mattermost_servicedesc$"
    }
    "--serviceoutput" = {
      "required" = true
      "value" = "$mattermost_serviceoutput$"
    }
    "--servicestate" = {
      "required" = true
      "value" = "$mattermost_servicestate$"
    }
    "--url" = {
      "description" = "mattermost incomming webhook url"
      "required" = true
      "value" = "$mattermost_url$"
    }
  }

  vars += {
    "mattermost_channel" = "$user.vars.channel$"
    "mattermost_hostalias" = "$host.display_name$"
    "mattermost_notificationtype" = "$notification.type$"
    "mattermost_oneline" = "$user.vars.oneline$"
    "mattermost_servicedesc" = "$service.display_name$"
    "mattermost_serviceoutput" = "$service.output$"
    "mattermost_servicestate" = "$service.state$"
    "mattermost_url" = "$user.vars.url$"
  }
}

object NotificationCommand "mattermost_host" {
  import "plugin-notification-command"

  command = [ PluginContribDir + "/icinga2-mattermost/mattermost.py" ]

  arguments = {
    "--channel" = {
      "description" = "mattermost channel"
      "required" = true
      "value" = "$mattermost_channel$"
    }
    "--hostalias" = {
      "required" = true
      "value" = "$mattermost_hostalias$"
    }
    "--hostoutput" = {
      "required" = true
      "value" = "$mattermost_hostoutput$"
    }
    "--hoststate" = {
      "required" = true
      "value" = "$mattermost_hoststate$"
    }
    "--notificationtype" = {
      "required" = true
      "value" = "$mattermost_notificationtype$"
    }
    "--oneline" = {
      "set_if" = "$mattermost_oneline$"
    }
    "--url" = {
      "description" = "mattermost incomming webhook url"
      "required" = true
      "value" = "$mattermost_url$"
    }
  }

  vars += {
    "mattermost_channel" = "$user.vars.channel$"
    "mattermost_hostalias" = "$host.display_name$"
    "mattermost_hostoutput" = "$host.output$"
    "mattermost_hoststate" = "$host.state$"
    "mattermost_notificationtype" = "$notification.type$"
    "mattermost_oneline" = "$user.vars.oneline$"
    "mattermost_url" = "$user.vars.url$"
  }
}
```
### 3. Create the mattermost User:
```
object User "mattermost" {
  import "generic-user"
  display_name = "Mattermost User"
  enable_notifications = true

  vars.channel = icinga /* Use here the url channel name, ie. without special characters and spaces */
  vars.url = https://mattermost.example.com /* Copy incoming Webhook URL defined in mattermost */
  vars.oneline = false /* Use true if you prefer having all output squashed on one line */
}
```
### 4. Apply notifications to mattermost User:
```
apply Notification "mattermost_service" to Service {
  assign where true
  command = "mattermost_service"
  users = [ "mattermost" ]
  period = "24x7"
  types = [ Problem, Acknowledgement, Recovery, Custom, FlappingStart, FlappingEnd, DowntimeStart, DowntimeEnd, DowntimeRemoved ]
  states = [ OK, Warning, Critical, Unknown ]
}
```

```
apply Notification "mattermost_host" to Host {
  assign where true
  command = "mattermost_host"
  users = [ "mattermost" ]
  period = "24x7"
  types = [ Problem, Acknowledgement, Recovery, Custom, FlappingStart, FlappingEnd, DowntimeStart, DowntimeEnd, DowntimeRemoved ]
  states = [ Up, Down ]
}
```

