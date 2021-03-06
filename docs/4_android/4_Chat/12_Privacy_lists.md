<span id="Privacy_lists" class="on_page_navigation"></span>
## Privacy lists

Privacy list API allows to enable or disable communication with other users in a chat. Privacy list API also gives possibility 
user to create, modify, or delete his privacy lists, define a default list.

To manage privacy lists SDK uses ```QBPrivacyListsManager```, to get it you can just call ```QBChatService.getInstance().getPrivacyListsManager()``` 
after success login to chat.

Also you can listen any changes in privacy lists, for that you can use ```QBPrivacyListListener```
```java
// set listener
QBPrivacyListListener privacyListListener = new QBPrivacyListListener() {
    @Override
    public void setPrivacyList(String listName, List<QBPrivacyListItem> listItem){
        //notify about setting privacy list with name 'listName' and inems 'listItem'
    }

    @Override
    public void updatedPrivacyList(String listName) {
        //notyfy about updating privacy list with name 'listName'
    }
};
```


### Retrieve privacy lists

User can have multiple privacy lists. To get a list of all your privacy lists', use the following request:
```java
QBPrivacyListsManager privacyListsManager = ...;
privacyListsManager.addPrivacyListListener(privacyListListener);

// get all privacy lists
List<QBPrivacyList> lists = privacyListsManager.getPrivacyLists();
```
Or you can get **single** privacy list **by name**:
```
QBPrivacyList list = privacyListsManager.getPrivacyList("public");
```

### Create a privacy list or edit existing list
To describe one item in privacy list this SDK uses ```QBPrivacyListItem``` model.

```QBPrivacyListItem``` model takes 4 arguments:
* **type** - use ```USER_ID``` to block a user in 1-1 chat or ```GROUP_USER_ID``` to block in a group chat.
* **valueForType** - ID of a user to apply an action
* **allow** - can be ```true/false``` (разрешить слать мне месседжи или нет).
* **mutualBlock** - to block user's message in both directions, can be ```true/false```.

A privacy list should have at least one element in order to create it.

**If no elements are specified then the list with given name will be deleted.
When you want to update a privacy list, you should include all of the desired items (i.e., not a "delta").**
```java
QBPrivacyList list = new QBPrivacyList();
list.setName("public");

ArrayList<QBPrivacyListItem> items = new ArrayList<QBPrivacyListItem>();

QBPrivacyListItem item1 = new QBPrivacyListItem();
item1.setAllow(false);
item1.setType(QBPrivacyListItem.Type.USER_ID);
item1.setValueForType(String.valueOf(3678));
item1.setMutualBlock(true);

items.add(item1);

list.setItems(items);

privacyListsManager.setPrivacyList(list);


```

**Pay attention, if you want to update or set new privacy list instead of current you should decline current default 
and active lists firstly:**
```java
QBPrivacyListsManager privacyListsManager = ...;

privacyListsManager.declineActiveList();
privacyListsManager.declineDefaultList();

//set new privacy list after
```

### Activate a privacy list

User can have multiple privacy lists, but default can be only one.
In order to activate rules from a privacy list you should set it as default.

```java
privacyListsManager.setPrivacyListAsDefault("public");
```

### Delete existing privacy list
You can delete privacy list by name:
```java
privacyListsManager.deletePrivacyList("public");
}
```

### Blocked user attempts to communicate with user

Blocked entities will receive an error when try to chat with a user in a 1-1 chat and will receive nothing in a group chat:

```java
QBChatDialog privateDialog = ...;
 
privateDialog.sendMessage("Hey man!");

...

privateDialog.addMessageListener(new QBChatDialogMessageListener() {
    @Override
    public void processMessage(String dialogId, QBChatMessage message, Integer senderId) {
 
    }
 
    @Override
    public void processError(String dialogId, QBChatException exception, QBChatMessage message, Integer senderId) {
        log("processError: " + error.getLocalizedMessage());
    }
});
    
```

Log output:
```
processError: Service not available.
```

