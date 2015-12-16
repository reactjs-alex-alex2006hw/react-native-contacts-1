# React Native Contacts (remobile)
A cordova contacts for react-native, supprt for ios and android

## Installation
```sh
npm install @remobile/react-native-contacts --save
```
### Installation (iOS)
* Drag RCTContacts.xcodeproj to your project on Xcode.
* Click on your main project file (the one that represents the .xcodeproj) select Build Phases and drag libRCTContacts.a from the Products folder inside the RCTContacts.xcodeproj.
* Look for Header Search Paths and make sure it contains both $(SRCROOT)/../../../react-native/React as recursive.

### Installation (Android)
```gradle
...
include ':react-native-contacts'
project(':react-native-contacts').projectDir = new File(rootProject.projectDir, '../node_modules/@remobile/react-native-contacts/android')
```

* In `android/app/build.gradle`

```gradle
...
dependencies {
    ...
    compile project(':react-native-contacts')
}
```

* register module (in MainActivity.java)

```java
import com.remobile.contacts.*;  // <--- import

public class MainActivity extends Activity implements DefaultHardwareBackBtnHandler {
  ......
  private RCTContactsPackage mContactsPackage; // <--- declare package
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mReactRootView = new ReactRootView(this);
    mContactsPackage = new RCTContactsPackage(this);// <--- alloc package

    mReactInstanceManager = ReactInstanceManager.builder()
      .setApplication(getApplication())
      .setBundleAssetName("index.android.bundle")
      .setJSMainModuleName("index.android")
      .addPackage(new MainReactPackage())
      .addPackage(mContactsPackage)              // <------ add here
      .setUseDeveloperSupport(BuildConfig.DEBUG)
      .setInitialLifecycleState(LifecycleState.RESUMED)
      .build();

    mReactRootView.startReactApplication(mReactInstanceManager, "ExampleRN", null);

    setContentView(mReactRootView);
  }

  ......

  // <----- add start
  @Override
  public void onActivityResult(final int requestCode, final int resultCode, final Intent data) {
      super.onActivityResult(requestCode, resultCode, data);
      mContactsPackage.onActivityResult(requestCode, resultCode, data);
  }
  // <----- add end
}
```

## Usage

### Example
```js
var React = require('react-native');
var {
    StyleSheet,
    View,
    Image,
} = React;


var Button = require('@remobile/react-native-simple-button');
var RCTContacts = require('@remobile/react-native-contacts');

var {
    contacts,
    ContactFindOptions,
    ContactField,
} = RCTContacts;

module.exports = React.createClass({
    testFind() {
        // display the address information for all contacts
        function onSuccess(contacts) {
            for (var i = 0; i < contacts.length; i++) {
                var item = contacts[i];
                console.log(item);
            }
        };
        function onError(contactError) {
            alert('onError!');
        };
        // find all contacts
        var options = new ContactFindOptions();
        options.filter = "";
        options.multiple = false;
        var fields = ["displayName", "addresses"];
        contacts.find(fields, onSuccess, onError, options);
    },
    testSave() {
        // create a new contact
        var contact = contacts.create();

        // store contact phone numbers in ContactField[]
        var phoneNumbers = [];
        phoneNumbers[0] = new ContactField('work', '212-555-1234', false);
        phoneNumbers[1] = new ContactField('mobile', '917-555-5432', true); // preferred number
        phoneNumbers[2] = new ContactField('home', '203-555-7890', false);
        contact.phoneNumbers = phoneNumbers;
        contact.nickname = "fang";

        // save the contact
        contact.save();
    },
    testRemove() {
        // display the address information for all contacts
        function onSuccess(contacts) {
            for (var i = 0; i < contacts.length; i++) {
                var item = contacts[i];
                item.remove();
            }
        };
        function onError(contactError) {
            alert('onError!');
        };
        // find all contacts
        var options = new ContactFindOptions();
        options.filter = "fang";
        options.multiple = true;
        var fields = ["nickname"];
        contacts.find(fields, onSuccess, onError, options);
    },
    pickContact() {
        contacts.pickContact(function(contact){
            console.log('The following contact has been selected:', contact);
        },function(err){
            console.log('Error: ' + err);
        });
    },
    chooseContact() {
        contacts.chooseContact(function(contact){
            console.log('The following contact has been selected:', contact);
        },function(err){
            console.log('Error: ' + err);
        });
    },
    newContactUI() {
        contacts.newContactUI(function(contact){
            console.log('The following contact has been selected:', contact);
        },function(err){
            console.log('Error: ' + err);
        });
    },
    render() {
        return (
            <View style={styles.container}>
                <Button onPress={this.testFind}>
                    testFind
                </Button>
                <Button onPress={this.testSave}>
                    testSave
                </Button>
                <Button onPress={this.testRemove}>
                    testRemove
                </Button>
                <Button onPress={this.pickContact}>
                    pickContact
                </Button>
                <Button onPress={this.chooseContact}>
                    chooseContact
                </Button>
                <Button onPress={this.newContactUI}>
                    newContactUI
                </Button>
            </View>
        );
    },
});


var styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: 'space-around',
        alignItems: 'center',
        backgroundColor: 'transparent',
    }
});
```

### HELP
* look https://github.com/apache/cordova-plugin-contacts


### thanks
* this project come from https://github.com/apache/cordova-plugin-contacts
