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
