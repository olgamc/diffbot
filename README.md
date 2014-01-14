#Diffbot API Octave Client

##Preface
The diffbot API Octave client provides a wrapper around the diffbot API, allowing Octave users to import data into their scripts easily. For the purpose of this document, the diffbot API client will be referred to simply as `diffbot`.

`Diffbot` uses [JSONlab](http://www.mathworks.com/matlabcentral/fileexchange/33381-jsonlab-a-toolbox-to-encodedecode-json-files-in-matlaboctave), which is a toolbox to encode/decode JSON files in MATLAB/Octave. You can use `diffbot` without having to learn JSONlab.

##Installation

1. Download and install [JSONlab](http://www.mathworks.com/matlabcentral/fileexchange/33381-jsonlab-a-toolbox-to-encodedecode-json-files-in-matlaboctave). 
   To install JSONlab, you simply need to unzip it to a folder, then add the folder to the path by using the following command:

   `addpath('/path/to/jsonlab');`

2. Install diffbot the same way. First, unzip diffbot into a folder. Then use the following command to add it to the path:

   `addpath('/path/to/diffbot');`
   
3. If you want to save these paths permanently, use `savepath();`

##Configuration

###Developer Token
In order to use the diffbot API, first you need to obtain a token. For a free token, go to http://www.diffbot.com/pricing/, and `Sign Up`. After you enter your name and email and `Place Order`, you will receive a developer token that will look something like this: 7edc4695g402e4d6e1071e0256e6223b. Use this value to set the global variable DIFFBOT_TOKEN in Octave:

`global DIFFBOT_TOKEN = "7edc4695g402e4d6e1071e0256e6223b";`

###Usage

Diffbot for Octave provides just one function: `diffbot`, which is used to make an API call and return an Octave structure containing the results.
```
[data success message] = diffbot(api_url, options)
   
OR
   
[data success message] = diffbot(api_url, "param1", value1, "param2", value2, ...)
   
input:
   api_url => the URL of the API, such as http://api.diffbot.com/v2/article.
   options => list of appropriate arguments for that API, and their values as specified in the diffbot API documentation.
   
output:
   data => an Octave struct that includes all fields returned by the API
   success => 1 if the request succeeded, 0 of failed
   message => error message received from the API if the request failed
```   
*The original API output is also saved in a file called result.txt*

Please refer to the official [diffbot API documentation](http://www.diffbot.com/products/) for more information regarding acceptable input and output parameters.

##Examples

###example1.m - call the Analyze API:

Set required variables:

```
% enter the token you received when you signed up on http://www.diffbot.com/
global DIFFBOT_TOKEN = "...";

% Analyze API URL
api = "http://api.diffbot.com/v2/analyze";

% url to analyze
url = "http://venturebeat.com/2013/11/22/diffbot-and-semantria-join-to-find-and-parse-the-important-text-on-the-net-exclusive/";
```

Make a call:

```
data = diffbot(api, "url", url);
```

Result is now loaded into the `data` structure and we can use it:

```
disp(data.type);
```

This example should output the following:

```
>> example1
article
>>
```

###example2.m - call the Article API:

Set required variables:

```
% enter the token you received when you signed up on http://www.diffbot.com/
global DIFFBOT_TOKEN = "...";

% Article API URL
api = "http://api.diffbot.com/v2/article";

% url to analyze
url = "http://venturebeat.com/2013/11/22/diffbot-and-semantria-join-to-find-and-parse-the-important-text-on-the-net-exclusive/";
```

Now we are ready to make a call:

```
% basic call
data = diffbot(api, "url", url);
```

OR

```
% specify some fields and return additional values for error handling
[data success message] = diffbot(api, "url", url, "fields", "meta,querystring,images(*)");
```

Result is now loaded into the `data` structure and we can use it:

```
if (success)
	disp(data.images)
else
	disp(message)
endif
```

This example should output the following:

```
{
  [1,1] =
  {
    primary = true
    url = http://venturebeat.files.wordpress.com/2014/01/benioff-in-nyc.jpg?w=31
1&h=150&crop=1
  }

  [1,2] =
  {
    url = http://venturebeat.files.wordpress.com/2014/01/screen-shot-2014-01-07-
at-3-15-45-pm.png?w=311&h=150&crop=1
  }
  ...
```

###example3.m - submit a basic crawl job:
Set required variables:

```
% enter the token you received when you signed up on http://www.diffbot.com/
global DIFFBOT_TOKEN = "...";

% Crawl API URL
api = "http://api.diffbot.com/v2/crawl";
```

Submit a basic crawl job. The job will be called sample_crawl. It will crawl www.diffbot.com and use Article API to process pages.
```
% Create a basic crawl job
[data success message] = diffbot(api, 
		"name", "sample_crawl",
		"seeds", "http://www.diffbot.com",
		"apiUrl", "http://api.diffbot.com/v2/article")
```

This example should output the following:
```
data =
{
  response = Successfully added urls for spidering.
  jobs =

  {
    [1,1] =
    {
      name = sample_crawl
      type = crawl
      jobStatus =
      ...
```
