# big-commerces
Reuseable Function

URL: [https://minhdong.mybigcommerce.com/](https://minhdong.mybigcommerce.com/)

Preview code: **6l3rpgazfo**

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

There are 3 options in the product setting which decide the "Add to cart" button


### Purchasability
- This product can be purchased in my online store (Add to cart Enabled). if Inventory = 0 || No tracking => Sold out.
- This product is coming soon but I want to take pre-orders (Pre-order Enabled | Doesn't care about inventory tracking).
- This product cannot be purchased in my online store (View more detail/Soldout enabled | Doesn't care about inventory tracking)

Ex:
- Can be purchased + Inventory = 0 = > https://minhdong.mybigcommerce.com/orbit-terrarium-small/
- Can be purchased + No-Inventory tracking => https://minhdong.mybigcommerce.com/orbit-terrarium-large/
- Coming soon (Pre-order) + Inventory > 0 => https://minhdong.mybigcommerce.com/laundry-detergent/
- Cannot be purchased => https://minhdong.mybigcommerce.com/fog-linen-chambray-towel-beige-stripe/
