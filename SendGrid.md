# Sending Emails Using SendGrid And Firebase Cloud Functions

## **What Is Happening**
SendGrid is a service that sends out personalised and functional emails based on your templetes.

We are utilising Firebase Functions in the backend to trigger an SendGrid API call whenever a document within Firebase is created/updated/deleted/etc.
*Note that to do this yoour project will need to be set up with at least a 'blaze plan' on Firebase*

The following examples will be for a contact form.

## **Set Up**

### **Install Dependencies**
#### Firebase and Firebase Functions
In the projects console type the following:
```
npm install firebase

npm install firebase functions
```

After Firebase Functions is installed change to the new functions folder in your project
```
cd functions
```

#### SendGrid
Once in the functions directory install the SendGrid dependencies
```
npm install @sendgrid/mail --save
```

#### Add your SendGrid API Key
Enter the following into the console to add your SendGrid API key as a hidden variable within Functions Config
```
firebase functions:config:set sendgrid.key=APIKEY
```

## **Creating The Firebase Function**
In the functions index.js add the following code.
Note that all console.log calls will appear in the Firebase Functions console. Not in the browser console.
```js
// Initialize the config
const admin = require('firebase-admin');
admin.initializeApp(functions.config().firebase);

// Create a variable of the API key you added to config
const SENDGRID_API_KEY = functions.config().sendgrid.key;

// Set up SendGrid
const sgMail = require('@sendgrid/mail');
sgMail.setApiKey(SENDGRID_API_KEY);
sgMail.setSubstitutionWrappers("{{", "}}");

// Create the function
exports.firestoreEmail = functions.firestore
  .document('messages/{messageId}') // Directory for the hook to look out for
  .onCreate((snap, context) => {
    const newMessage = snap.data(); // Create an object of the new document
    console.log(newMessage); // For debugging purposes only

    // Create the message object to send to SendGrid API
    const msg = {
      from: 'hello@reactfirebase.com', // This will appear in the from field of the email
      templateId: 'd-79f11386bc8d4d8fac70b8883eb93366', // The templete ID of the SendGrid email templete you want to utilise

      // All personalizations to be added to the email
      personalizations: [
        {
          // Who the email is being sent to
          to: [
            {
              email: "lachlan.mack9999@gmail.com"
            }
          ],
          // All substitutions to be made in the dynamic variables of the SendGrid email templete
          dynamic_template_data: {
            name: newMessage.name,
            company: newMessage.company,
            message: newMessage.message,
            email: newMessage.email
          }
        }
      ]
    };
    // Send the request to the SendGrid API
    return (sgMail.send(msg)
      .then(() => console.log('email sent!')) // Success handler
      .catch(err => console.log(err))); // Error handler
  });
```

### **Upload The Function To Firebase**
Once the function is complete it can be uploaded to firebase using the follwing command in the console
```
firebase deploy --only functions
```

## **Summary**
What should now happen is every time a document is added to the 'messages' collection (as in this example), the Firebase Function will activate and send an email through the SendGrid API.