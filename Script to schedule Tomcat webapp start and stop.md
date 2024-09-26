```tomcat.sh
#!/bin/bash
USERNAME="tomcat"
PASSWORD="tom"
CONTEXT_PATH="/zon"
STOP_TIME="10:20"
START_TIME="10:21"
start_command_executed=false
stop_command_executed=false
while true; do
  CURRENT_TIME=$(date +"%H:%M")
  if [ "$CURRENT_TIME" = "$START_TIME" ] && [ "$start_command_executed" = "false" ]; then
    echo "Starting application at $CURRENT_TIME..."
    curl -v -u "$USERNAME:$PASSWORD" "http://localhost:8080/manager/text/start?path=$CONTEXT_PATH"
    start_command_executed=true
  fi
  if [ "$CURRENT_TIME" = "$STOP_TIME" ] && [ "$stop_command_executed" = "false" ]; then
    echo "Stopping application at $CURRENT_TIME..."
    curl -v -u "$USERNAME:$PASSWORD" "http://localhost:8080/manager/text/stop?path=$CONTEXT_PATH"
    stop_command_executed=true
  fi
  echo "Current time is $CURRENT_TIME"
  sleep 1
done
```
