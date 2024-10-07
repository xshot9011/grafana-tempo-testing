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