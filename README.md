wak-tutorial-2014-12-12
=======================

Topics
------

* Model API (properties, scope, relations)
* Custom login
* Extended class
* Restricting query

Instructions
------------

1. Start solution
2. Run "CREATE_TEST_DATA"
3. Confirm that entities are created
4. Open Data Browser
5. Confirm that only WAF* classes are visible, and entites are displayed
6. Open index.waPage
7. Login with user name (created in CREATE_TEST_DATA) and "asdf" (hard-coded in required.js)
8. Confirm that only the user's data is visible.
9. Logout
10. Confirm that no data is visible

Advanced
--------
**Relations and extended data class

You might concider creating a relatedEntities attribute on the extended "1" class, which links to a relatedEntity attribute on the the extended "N" class.

![](https://github.com/miyako/wak-tutorial-2014-12-12/blob/master/images/NG.png)

The restricting query of the derived N class will look like this:

```
model.WAFProduct.events.restrict = function(e){
	return ds.WAFUser.products;
}
```

i.e. Execute the restring query of the derived 1 class,
and create a restricted collection by following the relation.

But this is **WRONG**!!!

In Wakanda, you can add a relatedEntities attribute to an extended class, but NOT a "relatedEntity" attribute, which needs to store the primary key of its related 1. In effect, it is a kind of a storage attribute. As such, the relatedEntity attribute must exist on the original class.

![](https://github.com/miyako/wak-tutorial-2014-12-12/blob/master/images/OK.png)

* before:
```
model.WAFProduct.user = new Attribute('relatedEntity', 
'WAFUser', 
'WAFUser', 
{
	'scope':'public'
});
```
* after:
```
model.Product.user = new Attribute('relatedEntity', 
'WAFUser', 
'WAFUser', 
{
	'scope':'public'
});
```
**Note**: This is a link from a "publicOnServer" class to a "public" class, but it is OK,  because the relatedEntity (Product.user) atrribute is "public", which means it is inherited by the derived class (WAFProduct.user).

**More**: If you decide to work with the original classes, on the server side, keep in mind that the data type of the foreign key is an extended class (WAFUser), which means you can't assign it's original class to create a related entity.

```
var product = new ds.Product();

product.user = ds.User(1);//WRONG!!! data type is not User
product.user = ds.WAFUser(1);//WRONG!!! the restrinting query will apply
product.user = ds.User(1).id;//GOOD
```
