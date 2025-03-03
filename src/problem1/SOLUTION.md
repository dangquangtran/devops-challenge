Provide your CLI command here:
grep '"symbol": "TSLA"' transaction-log.txt | grep '"side": "sell"' | awk -F'"' '{print $4}' | xargs -I {} curl -s "https://example.com/api/{}" >> output.txt


Explain:
grep '"symbol": "TSLA"' transaction-log.txt
Filter lines containing "symbol": "TSLA".

grep '"side": "sell"'
Continue filtering lines with "side": "sell".

awk -F'"' '{print $4}'
Extract order_id from JSON (4th position in string, between "...").

xargs -I {} curl -s "https://example.com/api/{}"
Use curl to send GET request with order_id.

>> output.txt
Write the results to the output.txt file.