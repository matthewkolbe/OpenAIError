# Automatic Exception Handling with OpenAI's API

To quote ChatGPT itself: "Code Injection Risk: Automatically writing code to files based on an AI's recommendation is risky and might lead to security vulnerabilities."

This library is purely for educational purposes, and should be treated as potentially dangerous. The purpose is to automatically gather debugging information, pass it on to an OpenAI GPT, and use its response to generate a new module to fix the issue that the GPT identified. While this technology fascinates and intrigues me, IT IS NONDETERMINISTIC, could potentially delete valuable intellectual property, could facilitate the singularity or worse.

## If you are foolish enough to want to use this code, keep this in-mind

You can pass in your own `OpenAI` client to the `OAIExceptionHandler` function. If you do not, it calls `client = OpenAI()`, which by default, tries to connect to the OpenAI API using the private key in your `OPENAI_API_KEY` environment variable. But the price of using this code is bourn by whatever account is linked to that environment variable. Got it? Good.

## What the code does

Provided you have access to an OpenAI API account, and have installed the prerequisites, and have acknowledged that THIS CODE DOES AN AUTOMATIC AI-GENERATED CODE INJECTION INTO YOUR CODE, then you might for example try wrapping your outermost `main()` function in a try-except block like this:

```
def main():
    try:
        MyProgram()
    except Exception as e:
        OAIExceptionHandler(e)
```

Whenever an exception is thrown that would have terminated the program, before termination, it gathers some data, passes it to OpenAI, wherein a GPT of your choosing recommends code changes, and those code changes are automatically implemented (while also saving the old file in case you want to revert).

Here's an example. I have a file called `OAIErrorExample.py`. It contains this code, which has a fatal exception due to a capitalization error in 'Name':

```
import json
from OAIError import OAIExceptionHandler

def get_name_from_json(json_string):
    data = json.loads(json_string)
    if 'name' in data:
        return data['name']
    if 'Name' in data:
        return data['name']
    
    raise KeyError(f"Could not find good candidate for 'name'")

def main():
    json_str_without_name = '{ "Name": "Leon", "Title": "Cleaner", "Age": 25, "City": "New York"}'
    try:
        print(get_name_from_json(json_str_without_name))
    except Exception as e:
        OAIExceptionHandler(e)

if __name__ == "__main__":
    main()
```

After I run this code, I get two files: `OAIErrorExample1704243000.495058.py`, which contains exactly the code I posted above, containing an error, and another one now called `OAIErrorExample.py`. This new file was automatically generated by GPT-4, and has this inside it:

```
# Corrected 'get_name_from_json' to handle case sensitivity in JSON key. Also fixed an improper call 'data['name']' when 'Name' key is present.

import json
from OAIError import OAIExceptionHandler

def get_name_from_json(json_string):
    data = json.loads(json_string)
    if 'name' in data:
        return data['name']
    elif 'Name' in data:
        return data['Name']
    else:
        raise KeyError(f"Could not find good candidate for 'name'")

def main():
    json_str_without_name = '{ "Name": "Leon", "Title": "Cleaner", "Age": 25, "City": "New York"}'
    try:
        print(get_name_from_json(json_str_without_name))
    except Exception as e:
        OAIExceptionHandler(e)

if __name__ == "__main__":
    main()
```
Now, when I rerun the same module, it prints out `Leon`, as expected. 