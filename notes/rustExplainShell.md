# Project Idea
We're going to create a rustLang binary that takes user input of a shell command we would like to know more about, makes a http(s) request to [ExplainShell](https://explainshell.com/#), fills in the query from user input, and returns a format in the terminal similar to what a user would get from the site itself. 


# Project Notes
## Steps
- read in user input from CLI
- sanitize and format user input
- append user input to explainshell query
- pass NEW string variable of explainshell website with query
- make http request
- check http response code
	- Continue on success//200
	- fail on anything else
- format html response inspired by @thingskatedid thread
- return formatted response to user.