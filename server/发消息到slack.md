    #!/bin/bash
    
    # nohup ./tail-slack.sh "/var/log/xxxw.log" "{webhook url}" "ERROR" &
    # tailing ERROR|Error|error|Cause:
    #tail -n0 -F "$1" | while read LINE; do
    #  (echo "$LINE" | grep -e "$3") && curl -X POST --silent --data-urlencode \
    #    "payload={\"text\": \"$(echo $LINE | sed "s/\"/'/g")\"}" "$2";
    #done
    
    # nohup ./tail-slack.sh "/var/log/jobpool-api_exception.log" "https://hooks.slack.com/services/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"  &
    # tailing ERROR|Error|error|Cause:
    tail -n0 -F "$1" | while read LINE; do
      (echo "$LINE") && curl -X POST --silent --data-urlencode \
        "payload={\"text\": \"$(echo $LINE | grep Exception | sed "s/\"/'/g")\"}" "$2";
    done
