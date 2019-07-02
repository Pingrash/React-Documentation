# Hook Up A Contact Form To Send Data To Firebase
## **What Is Happening**
The objective of this process is to upload the details entered by the user into a contact form, to Firebase.

A reason to do this is not only to store a record of the contact, but also can be paired with a firebase function to send off an email.

## **Set Up**

### **Install Dependencies**
#### Firebase
The only essential dependency for this process is firebase.
```
npm install firebase
```
Be sure to have Firebase initialised properly in index.js

Add firebase to your file
```js
const firebase = require('firebase');
```

### **Functions**
#### userTyping
A userTyping function can be used to update the state whenever the user types in a field.

This is commonly handled using a switch.

```js
userTyping = (type, e) => {
  // Not essential but handy for handling error properties for the form field
  this.setState({ errorType: null })

  switch (type) {
    case 'email':
      this.setState({ email: e.target.value });
      this.setState({ messageError: '' })
      break;

    case 'name':
      this.setState({ name: e.target.value });
      break;

    case 'company':
      this.setState({ company: e.target.value });
      break;

    case 'message':
      this.setState({ message: e.target.value });
      break;
  
    default:
      break;
  }
}
```

#### Helper Functions
There are potentially multiple helper functions that can be utilised depending on the form but in the case of a contact form only two are required.

##### validateEmail
Using regular expressions we can test what the email the user has entered into the form to check it's validity.

Returns true or false.

```js
validateEmail = (email) => /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(email);
```

##### messageValid
This function simply replaces all space characters (\s) and checks the length. 

Returns true or false.
```js
messageValid = (message) => message && message.replace(/\s/g, '').length;
```

#### submitMessage
This function handles uploading the contact message to firebase.

```js
submitMessage = async (e) => {
  // Prevent the page from refreshing
  // Redirect will be called later if needed
  e.preventDefault();

  // Create an object for the message based on what is set to the state
  const message = {...this.state};

  // Validate the email
  if (!this.validateEmail(this.state.email)) {
    this.setState({ messageError: 'Invalid Email', errorType: 'email' });
    return;
  }

  // Validate the message
  if (!this.messageValid(this.state.message)) {
    this.setState({ messageError: 'Invalid Message', errorType: 'message' });
    return;
  }

  console.log('sending message');

  // Send the message to Firebase
  await firebase
    .firestore()
    .collection('messages')
    .doc()
    .set({
      name: message.name,
      company: message.company,
      message: message.message,
      email: message.email
    })
    .catch(err => console.log(err));
  
  console.log('message sent');

  /*
    At this point you can redirect the user or set a flag in the state to display a success message
  */
}
```