# grafana-tempo-testing

- Keeping all lab and work here

https://grafana.com/docs/tempo/latest/traceql/

## Script Generate Trace

```sh
export endpoint=http://grafana-tempo.monitoring.svc.cluster.local:4318
export service_name=somchai
export main_trace_id=$(hexdump -vn16 -e '4/4 "%08X" 1 "\n"' /dev/urandom)
export main_span_id=$(hexdump -vn8 -e '4/4 "%08X" 1 "\n"' /dev/urandom)
export main_start_epoch=$(date +%s)

export subspan_start_epoch=$(date +%s)
export subspan_id=$(hexdump -vn8 -e '4/4 "%08X" 1 "\n"' /dev/urandom)
SLEEP_TIME=$((RANDOM % 20 + 1))
echo "SLEEP_TIME: $SLEEP_TIME"
sleep $SLEEP_TIME
export subspan_end_epoch=$(date +%s)
export duration=$(( $subspan_end_epoch - $subspan_start_epoch))
export span_name=test
python3 ./tracepusher.py \
    --endpoint=$endpoint \
    --service-name=$service_name \
    --span-name=$span_name \
    --trace-id=$main_trace_id \
    --parent-span-id=$main_span_id \
    --span-id=$subspan_id \
    --duration=$duration \
    --time-shif=true

export subspan_start_epoch=$(date +%s)
export subspan_id=$(hexdump -vn8 -e '4/4 "%08X" 1 "\n"' /dev/urandom)
SLEEP_TIME=$((RANDOM % 20 + 1))
echo "SLEEP_TIME: $SLEEP_TIME"
sleep $SLEEP_TIME
export subspan_end_epoch=$(date +%s)
export duration=$(( $subspan_end_epoch - $subspan_start_epoch))
export span_name=build
python3 ./tracepusher.py \
    --endpoint=$endpoint \
    --service-name=$service_name \
    --span-name=$span_name \
    --trace-id=$main_trace_id \
    --parent-span-id=$main_span_id \
    --span-id=$subspan_id \
    --duration=$duration \
    --time-shif=true

export subspan_start_epoch=$(date +%s)
export subspan_id=$(hexdump -vn8 -e '4/4 "%08X" 1 "\n"' /dev/urandom)
SLEEP_TIME=$((RANDOM % 20 + 1))
echo "SLEEP_TIME: $SLEEP_TIME"
sleep $SLEEP_TIME
export subspan_end_epoch=$(date +%s)
export duration=$(( $subspan_end_epoch - $subspan_start_epoch))
export span_name=deploy
python3 ./tracepusher.py \
    --endpoint=$endpoint \
    --service-name=$service_name \
    --span-name=$span_name \
    --trace-id=$main_trace_id \
    --parent-span-id=$main_span_id \
    --span-id=$subspan_id \
    --duration=$duration \
    --time-shif=true

export span_name=deploy-eks
export main_end_epoch=$(date +%s)
export duration=$(( $main_end_epoch - $main_start_epoch))
python3 ./tracepusher.py \
    --endpoint=$endpoint \
    --service-name=$service_name \
    --span-name=$span_name \
    --trace-id=$main_trace_id \
    --span-id=$main_span_id \
    --duration=$duration \
    --time-shif=true
```
<!-- http://grafana-tempo.monitoring.svc.cluster.local:4318 -->

export CI_PROJECT_NAME="somchai-platform"
export CI_JOB_NAME="kics_iac_scan"
export MAIN_TRACE_ID=$(hexdump -vn16 -e '4/4 "%08X" 1 "\n"' /dev/urandom)
echo "MAIN_TRACE_ID = $MAIN_TRACE_ID"
export CI_JOB_STAGE="test"
export CI_JOB_STATUS="success"
curl -X POST -H 'Content-Type: application/json' http://localhost:4318/v1/traces -d '
{
	"resourceSpans": [{
    	"resource": {
        	"attributes": [{
            	"key": "service.name",
            	"value": {
                	"stringValue": "'${CI_PROJECT_NAME}'"
            	}
        	}]
    	},
    	"scopeSpans": [{
        	"scope": {
            	"name": "self-managed-curl",
            	"version": "1.0.0",
            	"attributes": []
        	},
        	"spans": [
        	{
            	"traceId": "'${MAIN_TRACE_ID}'",
            	"parentSpanId": "EEE19B7EC3C1B109",
            	"spanId": "EEE19B7EC3C1B101",
            	"name": "'${CI_JOB_NAME}'",
            	"startTimeUnixNano": 1689969302000000000,
            	"endTimeUnixNano": 1689970000000000000,
            	"kind": 1,
            	"attributes": [
                    {
                        "key": "CI_JOB_STAGE",
                        "value": {
                            "stringValue": "'${CI_JOB_STAGE}'"
                        }
                    },
                    {
                        "key": "CI_JOB_STATUS",
                        "value": {
                            "stringValue": "'${CI_JOB_STATUS}'"
                        }
            	    }
                ]
        	}]
    	}]
	}]
}'


curl 5B8EFFF798038103D269B633813FC700
script 5A11F52C14D590330F5C4ECA0845DF43

traceID, spanID, parentSpanID, operationName {JOB}, serviceName {my.service}, kind {server}, statusCode {0}, statusMessage, instrumentationLibraryName {my.library}, instrumentationLibraryVersion {1.0.0}, traceState, serviceTags, startTime, duration, logs, references, tags

traceID, spanID, parentSpanID, operationName {Build Test }, serviceName {SomChai}, kind {Internal}, statusCode {1}, statusMessage, instrumentationLibraryName, instrumentationLibraryVersion, traceState, serviceTags, startTime, duration, logs, references, tags
