Introduction
State management is an important topic in mobile development. In Flutter, InheritedWidget makes the process of building the state management system easier. In this workshop, you will learn how to handle your app state by using InheritedWidget.

Overview
This workshop includes a series of steps to convert an app without any state management to a fully centralized state management system.

You will start with a demo Google store app. To see how the app looks, click the Run button in the top, right corner of the IDE.

Try to scroll through different google products, and add or remove products from the cart. The small cart icon on the right side of the app bar changes based on number of products in the cart. You can also perform search queries by clicking the search icon in the app bar and typing nest to filter nest products.

There are two components that require stateful objects:

The product list widget needs to store the items that are already in the cart and the text of the search query.
The cart icon needs to store the number of items in the cart.
Currently, this app stores duplicate information in the two components and must constantly sync the states. Let's see how we can improve this app!



Step 2

Define the state data structure
The first thing you need to do is to define a structure to store your app’s state. To do that, we must determine what’s considered to be the app’s state.

We know there are at least two components that require state: the product list and the cart icon.

In the source code, the product list corresponds to the ProductListWidget, and the cart icon corresponds to the ShoppingCartIcon.

Let's look at the ProductListWidget widget first. This widget builds the scrollable body and displays Google products. Because the widget needs to know which products to display and which products are already in the cart, the ProductListState stores the productList and the itemsInCart.

class ProductListWidgetState extends State<ProductListWidget> {
  List<String> get productList => _productList;
  List<String> _productList = Server.getProductList();
  set productList (List<String> value) {
    setState(() {
      _productList = value;
    });
  }

  Set<String> get itemsInCart => _itemsInCart;
  Set<String> _itemsInCart = <String>{};
  set itemsInCart(Set<String> value) {
    setState(() {
      _itemsInCart = value;
    });
  }

  // ...
}
On the other hand, the ShoppingCartIcon also stores itemsInCart because it needs to know number of products that are in the cart.

class ShoppingCartIconState extends State<ShoppingCartIcon> {
  Set<String> get itemsInCart => _itemsInCart;
  Set<String> _itemsInCart = <String>{};
  set itemsInCart(Set<String> value) {
    setState(() {
      _itemsInCart = value;
    });
  }

  //...
}
This is where things get interesting. Both ShoppingCartIcon and ProductListWidget store their own versions of itemsInCart, and the itemsInCart must be kept in sync. When the itemsInCart is updated in the ProductListWidget, the ProductListWidget also needs to update the itemsInCart in the ShoppingCartIcon. Things can can get out of hand very quickly if we added more widgets that depends on the itemsInCart.

Now, let's pull these states out of the widgets.

You can find the class by searching for TODO in the IDE

class AppState {
  // TODO: fill in this data structure.

  AppState copyWith({
    //..
  }) {
    // TODO: implement copy method.
  }
}

Step 3

Create an InheritedWidget
Now, we have our own data structure to store the states. The next thing we want to do is to place the data above the widgets that need it, so the entire widget subtree has access to the data.

This is where the InheritedWidget comes in handy. The widget has the ability to host the data for the subtree and notify the subtree to rebuild when the data changes.

The next step is to create our own InheritedWidget to host the data.

class AppStateScope extends InheritedWidget {
  // TODO: implement this class.

  static AppState of(BuildContext context) {
    // TODO: implement this method.
  }
  
  @override
  bool updateShouldNotify(AppStateScope oldWidget) {
    // TODO: implement this method.
  }
}

&&&&&&&&&&&&&&&&&&&&&&&&&&

Step 4

Create a StatefulWidget around the AppStateScope
The AppStateScope doesn't have state, it only hosts the data that it receives. We still need to create a StatefulWidget to store the data. The goal of this StatefulWidget is to create the AppState , provide APIs to modify the data, and host the data using the AppStateScope.

class AppStateWidget extends StatefulWidget {
  AppStateWidget({required this.child});

  final Widget child;

  static AppStateWidgetState of(BuildContext context) {
    // TODO: implement this method
  }
  @override
  AppStateWidgetState createState() => AppStateWidgetState();
}

class AppStateWidgetState extends State<AppStateWidget> {
  AppState _data = AppState(
    productList: Server.getProductList(),
  );

  void setProductList(List<String> newProductList) {
    // TODO: implement this method
  }

  void addToCart(String id) {
    // TODO: implement this method
  }

  void removeFromCart(String id) {
    // TODO: implement this method
  }

  @override
  Widget build(BuildContext context) {
    // TODO: implement this method
  }
}

&&&&&&&&&&&&&&&&&&&&&&

Step 5

Insert the AppStateWidget into the widget tree
Now, we’ll insert the AppStateWidgetState into the widget tree.

void main() {
  // TODO: insert AppStateWidget above MaterialApp.
  runApp(
    MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Store',
      home: MyStorePage(),
    )
  );
}

&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&

Step 6

Migrate the ShoppingCartIcon
Every widget should have access to the AppStateWidget and AppStateScope. Let's migrate the ShoppingCartIcon so that it uses AppStateScope instead of storing the state locally.

Note: The app won’t work properly until we migrate all the widgets.

// TODO: convert ShoppingCartIcon into StatelessWidget.
class ShoppingCartIcon extends StatefulWidget {
  ShoppingCartIcon({Key? key}) : super(key: key);
  @override
  ShoppingCartIconState createState() => ShoppingCartIconState();
}
We should also remove the global key usage.

// TODO: remove the usage of shoppingCart Globalkey.
final GlobalKey<ShoppingCartIconState> shoppingCart = GlobalKey<ShoppingCartIconState>();

&&&&&&&&&&&&&&&&&&&&

Step 7

Migrate the ProductListWidget
Let's do the same thing for the ProductListWidget so that it also uses AppStateScope.

// TODO: convert ProductListWidget into StatelessWidget.
class ProductListWidget extends StatefulWidget {
  ProductListWidget({Key? key}) : super(key: key);
  @override
  ProductListWidgetState createState() => ProductListWidgetState();
}
Remove the global key usage.

// TODO: remove the usage of productList Globalkey.
final GlobalKey<ProductListWidgetState> productList = GlobalKey<ProductListWidgetState>();

&&&&&&&&&&&&&&&&&&&&&&&&&&

Step 8

Migrate search TextField
We also need to migrate the search query so that it updates the AppState.

  void _toggleSearch() {
    setState(() {
      _inSearch = !_inSearch;
    });
    _controller.clear();
    // TODO: set productList to Server.getProductList().
  }

  void _handleSearch() {
    _focusNode.unfocus();
    final String filter = _controller.text;
    // TODO: set productList to Server.getProductList(filter: filter).
  }
  &&&&&&&&&&&&&&&&&&&&&&&&&
  
  Step 9
  
  Thank you for participating in this workshop
Congratulations! You successfully completed this workshop!

To learn more about InheritedWidget, visit the Flutter API document for InheritedWidget or the Flutter Widget 101 for InheritedWidget







