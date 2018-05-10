# udp/test

This document gives a brief description of installing Postman, setting
up an environment for using UDP, and importing a collection of UDP
requests and tests.  This was written based on Postman v5.5.3.  If a
newer version of Postman is being used, these instructions may be
outdated.  As always, be familiar with the official Postman documentation. 

## Requirements
1. Postman (https://www.getpostman.com/), version 5.5.3 or greater.
1. UDP authorization token and parameters for the requests in this collection.

## Preparation
1. Download and install Postman.  Signing up for a Postman account is
recommended. Postman will automatically back up your work to your online
account as you go along.
1. Download the `udp.postman_collection.json` file contained in the same
directory as this document.

## Create an Environment
1. In the upper-right part of Postman's main window you will see a menu titled
"No Environment" and a button to the right of it with an eye icon.  That's the
"Environment quick look button".  Click the quick look button.

1. In the panel that appears, the top section will be titled "Environment".
The content of the section should say "No active Environment".  Click the
"Add" link in the upper-right part of that panel.

1. A "Manage Environments" dialog will appear with a "Add Environment"
heading.  The cursor will be flashing in the "Environment Name" field.
Enter a name for the environment you'll create.  A name like
"`udp _username_`" is recommended, where _username_ is the name of the
user from which the events in will be sent.

1. In the heading of the "Key" and "Value" table below the name field, click
the "Bulk Edit" link near the right side.

1. Paste the following into the text field:

    ```text
    udp_token:64_digit_hexadecimal_authorization_token
    udp_uniqname:test_user_uniqname_here
    udp_lis_course_offering_sourcedid:test_course_SIS_ID
    udp_lis_person_sourcedid:test_user_SIS_ID
    gcp_token:authorization_token
    ```
    
    Replace the values as appropriate.  Ideally, the test user uniqname
    and SIS ID should correspond to the same user.
    
1. Click the "Add" button near the lower-right corner of the dialog box. 

1. When the list of environments is shown, close the dialog box.

1. In the upper-right part of Postman's main window change the menu titled
"No Environment" to show the name of the environment you just created.  To
verify the environment is active, click the quick look button.  The panel
that appears should show the values you entered.  Click the button again to
dismiss the panel.

## Import the Collection
1. Click the "Import" button near the upper-left corner of the Postman main
window.

1. In the "Import" dialog box that appears, the "Import File" tab should be
active by default.  Click the "Choose Files" button to choose the
`udp.postman_collection.json` file you downloaded or drag that file into the
dialog box.

    (The "Import from Link" tab also works well if you're able to give a
    URL to the raw form of the `udp.postman_collection.json` file.)
    
## Run the Collection
1. Click the "Runner" button near the upper-left of the main Postman window.
A new window will open.

1. Select "udp" in the collection list and the name of the environment
created earlier in the "Environment" menu.

1. Click the "Run udp" button.  The requests will be run and a summary of
their test results will be shown. 

## Run a Single Request
1. Click the name of the collection in the list on the left side of the main
Postman window.  It should expand to show a list of the requests in the
collection.

1. Click the name of the "SessionEvent, LoggedIn" request to open it in a
new tab. 

1. Click the "Send" button.  The response body will be shown by default.
Click on the "Test Results" tab to see a summary of the tests for that
single request.

## CLI

Postman provides a NodeJS-based CLI for running collection tests, called
`newman`.  ("Hello, Newman.")

1. Install `newman`:

    ```
    npm install newman --global
    ```

1. Create a file with the UDP authorization token as a Postman global
variable:

    (Replace `64_digit_hexadecimal_authorization_token` with actual UDP
    authorization token.)

    ```
    mkdir -p ~/.postman
    cat > ~/.postman/udp-authz.postman_global.json <<EOT
    {
      "name": "udp-authz",
      "values": [
        {
          "enabled": true,
          "key": "udp_token",
          "value": "64_digit_hexadecimal_authorization_token",
          "type": "text"
        }
      ],
      "_postman_variable_scope": "global"
    }
    EOT
    ```
    
1. Run `newman` using the name of the collection, the authorization token
from the global variable file, and the user/course values from an
environment variable file:    

    ```text
    newman run udp.postman_collection.json \
      -g ~/.postman/udp-authz.postman_global.json \
      -e udp-ststvii.postman_environment.json
    ```
    
    **_Note_**:  Unlike typical CLI programs, `newman` requires the name
    of the input file (`udp.postman_collection.json`) **_before_** the
    options (`-g` and `-e`).
    
## GCP Authorization token generation

1. Start a terminal window and install Google-cloud-SDK (https://cloud.google.com/sdk/docs/quickstart-macos)
 1. to install Google cloud SDK the important step to follow  is `Before you Begin` list.
    1. test if gcloud is installed or not something as below
         ```
         08:11:26-user1~$ gcloud --version
         Google Cloud SDK 192.0.0
         bq 2.0.29
         core 2018.03.02
         gsutil 4.28
        ```  
2. grab the service-account file from Unizin (https://umich.app.box.com/notes/273806901933) give it `your-fav-file-name.json` 
   and put it a directory as you wish
3. Set an environment variable: `export GOOGLE_APPLICATION_CREDENTIALS="/home/user/Downloads/service-account-file.json"`
4. start a new terminal to pick up the service account generate OAuth token with `token_generator.sh` script as shown below. 
   The token is good for one hour.
   
         08:22:59-user~/src/myprojects/gcp$ ./token_generator.sh
        
           ya29.c.YYYYyyeyeyeyeyeyeyeyeYYYYYyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy
            
5. Alternatively you can grab the token from command line using
 `GOOGLE_APPLICATION_CREDENTIALS=~/<path-to-service-accout.json> gcloud auth application-default print-access-token`
   

5. Grab the `access token` value from above and place it in the environmental variables `gcp_token` in the Postman along with others.

Token generation set up process is a seperate process from running the Postman script and token expire in 1hour so you have to generate the token again. 

## GCP Token generation using Openshift
I haven't found an obvious way to tie up the service account.json file with postman for making the token generation process
automatic. There is a process in place you can get the token going to Openshift and following below steps
1. Go to Openshift project [tl-hacks#gcp-token-generator](https://openshift.dsc.umich.edu:8443/console/project/tl-hacks/browse/pods/gcp-token-generator-6-mqw36?tab=terminal)
2. In the terminal window type `./token.sh` this will generate a GCP bearer token that can be populated in Postman
environmental variable `gcp_token`

## importing Environmental variable file
Postman provides an easy way to import the enviromental variables that are used by Postman when making the API call. Here the steps to do it
1. Click on the "Manage Environments" button with a gear icon (⚙️) is located in the upper right corner of the main Postman window, 
2. Click on the `import` button on the pop-up window, that will ask to upload a file choose the file the file `udp-ststvii.postman_environment.json`
3. add `udp_token` and `gcp_token` values


## Run the Postman test from Teams Workspace
All postman tests are based from this [document](https://docs.google.com/spreadsheets/d/1XGj76VRC1t2NH3LeA60U2rHqogqx_CIy1KAL48Cv5NQ/edit#gid=0)
some of naming convention in postman tests taken as the way they are described from the document.

1. Open the Postman desktop app on your machine, it will defaults to `My Workspace` click on the dropdown next to it and 
select `Team` UDP (UMich ITS TL) workspace. You may see various postman collection but collection of interest is `udp`
2. select the Environment `udp_stsvii_env` and grab the gcp taken from openshift as described in above section 
3. Click on the `Runner` button and that will pop open another window with in postman context and choose 
    1. `udp` collection, the collection would have multiple folders categorizing a set of test scenarios testing
    2. select the Enviroment as `udp_stsvii_env` 
    3. Hit the big blue button that says 'Run udp', this will run all the tests in the collection
    4. you can just run specific test case as you want by selecting sub folder underneath the main collection
           1. for example, select `udp` collection and sub collection`Caliper Compliance` and repeat step 2 and 3
           2. this will run just test cases associated with caliper compliance test.
4. For debugging purposes, you can have the postman console open for more info in case of error
    1. Open Postman app, go to `View` => `Show Postman Console`, this will open the console and let the window open while
    running the test
5. Errors associated with token expiry can be understood by 401 Not Authorized, as alway start with a fresh token when running the collection
