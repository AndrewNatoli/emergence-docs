##Preface

This page will explain how to utilize the "Model" portion of Emergence's [Model-View-Controller](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) pattern.

MVC is a way of organizing code into three distinct partitions. These three partitions are data storage (Model), templates (View), and code logic (Controller).

The MVC pattern is designed to provide you with a way to do data storage, retrieval, and modification with as little SQL as possible. However if you want to run raw queries you still can utilizing the DB class.

Emergence uses a Virtual File System to maintain websites so your site is most likely going to be inheriting itself from the base website, skeleton.mics.me.

Skeleton should come with two built in Emergence classes called ActiveRecord and VersionedRecord. These classes utilize the [Active record pattern](http://en.wikipedia.org/wiki/ActiveRecord) which allows SQL-less development for most cases.

To make a model in Emergence you will need to simply create a new PHP class that extends ActiveRecord. ActiveRecord requires a few variables for configuration such as the name of the SQL table and the fields for the model. Don't worry Emergence has a tool to that automatically makes your tables after you are done making the class.

If you need to make a versioned model you may extend your class with VersionedRecord instead of ActiveRecord. Since VersionedRecord extends ActiveRecord itself you still need all the configuration variables that are part of ActiveRecord however VersionedRecord adds one more. You must also provide a name for the SQL table which will store old versions of your model's objects (the history table) while maintaining the current version in the model's main SQL table.

##Making Models
To start making an Emergence model you will need to make a new PHP class on your site.

To make a model you should create a new file in the php-classes folder. The name of the file should be the same as the name of the class but with .class.php added to the end. So for example for a class name 'Example' the filename would be 'Example.class.php'.

####When creating new classes the name and the filename must match up in order for the PHP class auto loader to work properly. It is case-sensitive.

The class won't be loaded until PHP actually encounters it in the controllers.

###We can start with the following code for our Example model.
```php
    <?php
	class Example extends ActiveRecord {
		// ActiveRecord configuration
		static public $tableName = 'examples'; // the name of this model's table
		
		// controllers will use these values to figure out what templates to use
		static public $singularNoun = 'example'; // a singular noun for this model's object
		static public $pluralNoun = 'examples'; // a plural noun for this model's object
		
		// the lowest-level class in your table requires these lines,
		// they can be manipulated via config files to plug in same-table subclasses
		static public $rootClass = __CLASS__;
		static public $defaultClass = __CLASS__;
		static public $subClasses = array(__CLASS__);
	
		// gets combined with all the extended layers
		static public $fields = array(
			'Title'
		);
	}
```

The above example is the minimum amount of code necessary to create a new model. Now you can simply use the Table Manager utility to make a table for the model.

To do this simply visit **http://yoursite.com/table_manager**. It might ask you to login but after that you should see a form that looks like the image below.

!["Emergence Table Manager"](http://emr.ge/img/tutorials/models/1.png "Emergence Table Manager")

From this you can now select your model on the left side and click on the "Generate CREATE TABLE SQL" button. After clicking the button you'll see the following dialog.

!["Emergence Table Manager - Create Table"](http://emr.ge/img/tutorials/models/2.png "Emergence Table Manager - Create Table")

If you have SQL access and have a sufficient enough background to make custom changes to SQL tables you can simply manually copy this generated SQL text and run it on your own in a SQL client like phpMyAdmin or you can simply click on the "Execute SQL" button to make the table with the site's own connection to the SQL database.

####You might have noticed that the generated SQL table contains extra fields.

These fields are:

1. ID - Automatically incremented ID.
2. Class - The name of the class to instantiate the class as. Similar classes can share tables. 
3. Creator - The ID of the creator. This is sometimes assumed as the user currently logged in but can be manually set. 
4. Created - A SQL timestamp field that is set to the current timestamp when a new record is created.

These fields come with every ActiveRecord or VersionedRecord model.

Once the table is created the Model is ready to use.

##Configuring Models
When creating an ActiveRecord model one must first choose whether or not they need to have the model versioned. A versioned model will use an extra SQL table to store the history of every create, edit, or delete of records within that model. A configuration of an ActiveRecord model requires the definition of the model's SQL table, a singular noun and plural noun describing the name of said model as well as the fields that are part of the model. A versioned model will also require the definition of the name of the SQL history table. The name of the model is derivied from the name of the class.

###Defining a Model's Fields
A model's feidls are defined in the fields array. The array's key will be the name of the field within the model as well as the column name where the field's data will be stored in the SQL table. You may use a simple string to specify what type to use for the field or an associative array which may contain other field options. Not specifying any type will default to a varchar (255) in SQL. The chart below explains the various allowed field types as well as any allowed configuration options available to that field type.


####Field Types

| _Field Type_    | _Field Aliases_ | _Use for_ |
| ------------- |:--------------|:-------|
| String        | clob, string	| Text data. |
| Boolean       | boolean	    | A single value of true or false. |
| Decimal       | decimal	    | A number value containing a decimal point. |
| Integer       | int,uint,integer |	A numerical value consisting of a single integer. Remember integers are whole numbers containing no decimal point. |
| Timestamp     | timestamp	    | A value containing a fully qualified date and time. You may set the value as either a Unix timestamp or a string following ISO 8601 standard. |
| Date          | date          | A value containing a fully qualified date. You may set the value as either a Unix timestamp or a string following the date portion of the ISO 8601 standard. |
| Serialized    | serialized    | Any PHP value that can be serialized without losing precision. |
| List          | set,list      | A PHP array stored in the database as a string delimited by a comma by default. May not be a multidimensional array. |


####Modifying a Model
#####You may modify a model at any time after it's table has been created however you must manually modify the structure of the table in SQL. You may of course delete the table and recreate it with the table manager utility. If you are doing this to a versioned model make sure you make the modifications to the history table as well the original.

##Starting to Build Stuff
Now that you have a defined model as a class in PHP and as a defined table in SQL you can begin to use it in your executed PHP code. This part of the code is usually called the controller.

#####It is usually considered bad practice to write our model controller code directly in a site-root php file but for now we can start by just making an example.php in site-root. It is highly recommended that you use one of Emergence's built in controllers such as RecordsRequestHandler or CRUDRequestHandler but for the sake of this tutorial we will be doing all our work directly in a site-root php file.

####Creating a New Record
There are a couple ways to create a record.

Here is how to do it from a blank record that you can manually fill out the attributes of before saving.
```php
    <?php
    
    $object = new Example();
    $object->Title = 'i am a string.';
    $object->save(); // runs dynamic public function save
    
    echo $object->ID; // the automatically assigned ID of this object in SQL
    // output: 1, 2, 3, 4, etc everytime you run lines 3 through 5
```

Sometimes you'll know what all the fields are right away. For that there's another method of filling out the fields immediately.

```php
    <?php
    
    // create and save at the same time
    $object = Example::create(array(
        'Title'	=>	'i am a string.'
    	,'CreatorID'	=>	1
    ),true); // <|-- This boolean(autoSave) is false by default, you will need to uncomment the save() line below if you omit this option or set it to false
    
    //$object->save();
    
    echo $object->ID; // the automatically assigned ID of this object in SQL
```

####Editing a Record
Of course there's also multiple ways of editing a record.
```php
    <?php
    
    $object = Example::getByID(1); // returns an instance of an Example record
    $object->Title = 'i am a string too.';
    $object->save(); // runs dynamic public function save
```

There's also a way of changing multiple fields in an existing object with only one array input.
```php
    <?php
    
    $object = Example::getByID(1); // returns an instance of an Example record
    $object->setFields(array(
        'Title'	=>	'i am a string.'
    	,'Creator'	=>	1
    ));
    $object->save(); // runs dynamic public function save
```

####Deleting a Record
```php
    <?php
    
    $object = Example::getByID(1); // returns an instance of an Example record
    $object->destroy(); // runs dynamic public function destroy
```

##Retrieving Data
There are multiple ways to find and retrieve records from your database. The functions designed for this exist as public static methods built into ActiveRecord. This means that all Emergence models automatically have these methods.

###ActiveRecord Retrieval Methods

There are two types of methods for retrieving data.

* Single Record methods will return the first record only.
* Multiple Record methods will return an array of records.

In this context a record is an instantiated ActiveRecord object in PHP.

####Single Record
* getByContext
* getByContextObject
* getByField
* getByHandle
* getByID
* getByQuery
* getByWhere

####Multiple Records
* getAll
* getAllByClass
* getAllByContext
* getAllByContextObject
* getAllByField
* getAllByQuery
* getAllByWhere

####Examples
```php
    <?php
    
    // Simple WHERE: `column` = "value" (escaping is automatic)
    foreach(Example::getAllByField('CreatorID', 7) AS $object)
    {
        echo "Found record #$object->ID: $object->Title<br>";
    }
    
    // Advanced WHERE: array of conditions get combined with AND
    $conditions = array(
    	'CreatorID' => 8  // key => value pairs get turned into `key` = "value" (escaping is automatic)
    	,'Title LIKE "TIL%"' // keyless strings get included as-is (you must escape user values! - see DB::escape)
    );
    
    foreach(Example::getAllByWhere($conditions) AS $object)
    {
    	echo "Found record #$object->ID: $object->Title<br>";
    }
    
    // Custom query: do whatever you want as long as it SELECTs * from the model's table
    $sql = 'SELECT * FROM examples JOIN people ON people.ID = examples.CreatorID WHERE people.Email LIKE "%@emr.ge"';
    foreach(Example::getAllByQuery($sql) AS $object)
    {
    	echo "Found record #$object->ID: $object->Title<br>";
    }
```