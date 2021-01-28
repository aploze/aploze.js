# Aploze Client JS Library

## Why ?

- Create the App iFrame on your live page (Your body should be empty)

- Link your cart with Aploze by a two-way communication between the parent page and the iFrame, and redirect the user to your checkout page when the user validate his Aploze cart.

- Track created carts during your checkout steps

## Installation

To install Aploze library, add the script tag in the head of your website.

```html
<script>
	(function (a, p, l, o, z, e, js) {
		a[o] = a[o] || function () { (a[o].q = a[o].q || []).push(arguments) }
		;(e = p.createElement(l)), (js = p.getElementsByTagName(l)[0])
		e.id = o;e.src = z;e.async = 1;js.parentNode.insertBefore(e, js)
	})(window, document, 'script', 'Aploze', 'https://app.aploze.com/aploze.js')
</script>
```

## Usage

To use Aploze library, you need a Channel ID.
Please ask us if you don't have one.

### Initialize App with my channel ID

```javascript
Aploze('init', {
	id: 'YBKWdhAAACMAFLD7', // Your Channel ID
	liveUrl: 'https://yourwebsite.com/live', // Your website live page URL (where the iFrame need to be created)
	env: 'staging', // 'prod' or 'staging' (default: 'prod')
	debug: true, // enable logs (default : false)
	hasTracking: false, // disable tracking (default: true)
	params: {
		status: 'live', // force live status
		fakestream: true // enable fake video stream
	}
})
```

### Use Aploze Events

The First way of communication is to listen what's happening on Aploze App.
Here a list of events that you can subscribe to :

#### it works

`app.ready` : The app is ready

`cart.update` : The cart is updated during live & replay

`cart.validate` : The cart is validated by the user during live & replay

#### coming soon

`chat.message` : A new message is shown in chat during live

`chat.send` : The user send a new message during live

`stories.change` : The user browse stories during replay

`ui.like` The user send a like during live



#### How to listen events ?

##### - `on`
Attach a handler to an event.
The handler is executed

```javascript
Aploze('on', 'app.ready', function () {
	console.log('App is now ready')
})
```

##### - `once`
Attach a handler to an event.
The handler is executed once, and removed

```javascript
Aploze('once', 'app.ready', function () {
	console.log('App is now ready')
})
```

##### - `off`
Remove a handler.

```javascript
var appReadyHandler = function () {
	console.log('App is now ready')
}

Aploze('on', 'app.ready', appReadyHandler)
// ...
Aploze('off', 'app.ready',  appReadyHandler)
```

### Send change to Aploze

The Second way of communication is to send your changes to Aploze App.
It's useful to keep carts synced in some cases, and mendatory to track all converted carts in checkout steps

#### it works

##### - `cart.sync`

- The user has already a cart on your website (with LiveShopping Products), so you need to communicate the current cart state after App initialization in order to sync with the aploze Aploze Cart.
- The user don't have cart on your website, but you receive a new cart from Aploze, so you beed to communicate your new cart ID.
- The user has updated the cart on your website (deletation, quantity and/or variants change) so the Aploze cart needs to be updated.

```javascript

var wellFormatedCart = {
	id: 4670487234, // Cart ID
	items: [
		{
			id: 74590366,
			quantity: 1
		},
		{
			id: 74590370,
			quantity: 3
		}
	]
};

Aploze("submit", "cart.sync", {
	datas: wellFormatedCart,
	callback: function(response){
		if (response.error) {
			console.log('error', response)
		} else {
			console.log('success', response)
		}
	}
})
```

##### - `product.disable`

- You receive a cart update by listening the Aploze `cart.update` event, but a new product is not available or out of stock. Aploze App needs to know which product is not available to prevent the user to add it again.

```javascript
var outOfStockProduct = {
	id: 74590366 // Product ID
}

Aploze("submit", "product.disable", {
	datas: outOfStockProduct,
	callback: function(response){
		if (response.error) {
			console.log('error', response)
		} else {
			console.log('success', response)
		}
	}
})
```

#### coming soon

##### - `cart.buy`

- You receive a cart validation by listening  `cart.validate`, so you need to redirect the user to your checkout page and let him fill his delivery and payments information. At the end of the checkout, when a cart is converted to an order, I mean when the products of this cart are bought, Aploze needs to be alerted, in order to know exactly the conversion rate of the Liveshopping operation.

```javascript
var completedCheckoutCart = {
	id: 4670487234
}

Aploze("submit", "cart.buy", {
	datas: completedCheckoutCart,
	callback: function(response){
		if (response.error) {
			console.log('error', response)
		} else {
			console.log('success', response)
		}
	}
})
```
