# Easily Build JSON strings in bash

I had to create a long json string for a REST payload. Doing it by string manipulation was painful. Instead of mainpulating string and worrying about single and double quote, following method is way easier to format and generate json strings.

```
name='Jason'
address='California'
phone='+123'
zip='1111'
json=$(cat <<-END
    {
	    "name": "$name",
	    "address": "$address",
	    "phone": "$phone",
        "zip": $zip
    }
END
)

>> echo $json
{ "name": "Jason", "address": "California", "phone": "+123", "zip": 1111 }

```
To remove any line terminators in json, use translate `tr` utility,

```
json=$(echo "$json" | tr -d '\n' | tr -d '\r' )
```
