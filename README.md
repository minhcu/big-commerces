# big-commerces
Reuseable Function

## Price function

```javascript
function reverseDisplayPrice(price) {
    var arr = price.split(' ');
    var temp = arr[0];
    arr[0] = arr[1];
    arr[1] = temp;
    return arr.join('');
}
```

## Continue Selling

There are 3 options in the product setting which decide the "Add to cart" button (Doesn't care about inventory tracking)


### Purchasability
- This product can be purchased in my online store (Add to cart Enabled)
- This product is coming soon but I want to take pre-orders (Pre-order Enabled)
- This product cannot be purchased in my online store (View-detail enabled)
