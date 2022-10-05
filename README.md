# big-commerces support
Reuseable Function

URL: [https://minhdong.mybigcommerce.com/](https://minhdong.mybigcommerce.com/)

Preview code: **6l3rpgazfo**

## Price function (due to Price didn't display right)

Price format should be **priceUnit + price**

Ex:
- $42.00
- đ42.00

Used in: Everytheme.


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

There are 2 setting in the product setting which decide the "Add to cart" button

- **Inventory**
- **Purchasability**

### Inventory
- No tracking -> I = -2147483648
- 0 in stock -> I = 0 (API return product.available = 0)
- > 1 in stock -> I = 1 (API return product.available = -2147483648)

-> isAvailable = !!I

### Purchasability
- This product can be purchased in my online store (Add to cart Enabled) -> P = false
- This product is coming soon but I want to take pre-orders -> P = true
- This product cannot be purchased in my online store -> P = false

There are no continueSelling setup, therefore we should use isPreorder.

### How it should work

- isSoldOut = !P && !isAvailable

Ex:
- Can be purchased + Inventory = 0 = > https://minhdong.mybigcommerce.com/orbit-terrarium-small/
- Can be purchased + No-Inventory tracking => https://minhdong.mybigcommerce.com/orbit-terrarium-large/
- Coming soon (Pre-order) + Inventory > 0 => https://minhdong.mybigcommerce.com/laundry-detergent/
- Cannot be purchased + Inventory > 0 => https://minhdong.mybigcommerce.com/fog-linen-chambray-towel-beige-stripe/

Bigcommerce will return these for product setting


```javascript

const product = {
    has_options: options.length > 0 ?,
    pre_order:  isAbleToPurchased,
}

```

How flags work:
- Can be purchased P.false + No-Inventory tracking I.true => flags = 1
- Can be purchased P.false + Inventory = 0 I.false => flags = 0
- Can be purchased P.false + Inventory > 0 I.true => flags = 1


- Can NOT be purchased => available = 0; flags = 0;

- Pre-order P.true + No-Inventory tracking I.true => flags = 1
- Pre-order P.true + Inventory = 0 I.false => flags = 0
- Pre-order P.true + Inventory > 0 I.true => flags = 0

Remodify search-results_items.js
```javascript
if (selectedVariant) {
    productAvailable = selectedVariant.available;
    continueSelling = selectedVariant.flags & 1;
    // available === -2147483648 when the variant or product is marked as No tracking inventory.
    isAvailable = productAvailable > 0 || productAvailable === -2147483648;
}
else {            
    // aggregrate the available field from all variants.
    productAvailable = 0;
    for(var i = 0; i < variants.length; i++){
        var v = variants[i];
        // no inventory tracking?
        if (v.available === -2147483648){
            // we set total available as unknown and isAvailable = true
            productAvailable = -2147483648;
            continueSelling = selectedVariant.flags & 1; //Replace 
            isAvailable = true;
            break;
        } else {                        
            // add available
            productAvailable += v.available;
            // isAvailable is only set when v.available > 0
            if (v.available > 0)
                isAvailable = true;

            // continue selling when sold out?
            if ((v.flags & 1)) {
                continueSelling = true;
            }
        }
    }
}


t.available = productAvailable;
t.hasDiscount = hasDiscount;
t.continueSelling = continueSelling;
t.isSoldOut = !continueSelling && !isAvailable;

```

## Custom Swatches

Used in theme:
- Annie's Garden

```javascript
var UsfSwatches = {
        props: {
            product: Object,
        },
        data() {
            var product = this.product;
            var isHaveOption = product.options.length > 0;
            var optionsExtra;
            var optionColor;
            // Get the color/colour option and append it to option - option_index
            for (let i = 0; i < product.options.length; i++) {
                var o = product.options[i];
                var downcased_option = o.name.toLowerCase();
                if (downcased_option == "color" || downcased_option == "colour") {
                    optionColor = o;
                    optionsExtra = product.extra.optionsExtra[i].valueIds;
                    break;
                }
            }
            return {
                product: product,
                isHaveOption: isHaveOption,
                optionColor: optionColor,
                optionsExtra: optionsExtra,
            };
        },
        template:`
            <div v-if="isHaveOption" :class="'card-option card-option-' + product.id">
                <div v-if="optionColor" class="form-field">
                    <label class="form-option form-option-swatch" v-for="(v, index) in optionColor.values" :data-product-swatch-value="optionsExtra[index]">
                        <span class="form-option-tooltip" v-html="v"></span>
                        <span class="form-option-variant form-option-variant--color" :title="v" :style="'background-color: ' + v"></span>
                    </label>
                </div>
            </div>
        `
    };
    usf.register(UsfSwatches, null, "usf-swatches");
```

## Custom product description (due to product api return null)

Used in theme:
- Annie's Garden

```javascript
    var UsfCardDesc = {
        props: {
            product: Object,
        },
        data() {
            var product = this.product;
            var productDescript;
            return {
                product: product,
                productDescript: productDescript,
            }
        },
        beforeMount() {
            if (!this.product.description) {
                var id = this.product.id;
                usf.utils.send({
                    url: usf.settings.searchSvcUrl + 'products',
                    data: {
                        apiKey: usf.settings.siteId,
                        id: id,
                        fields: 'description'
                    },
                    success: r => {
                        r = JSON.parse(r);
                        console.log(r);
                        return this.productDescript = r.data.description;
                    },
                });
            }
            else {
                this.productDescript = this.product.description;
            }
        },
        template: `
            <div class="card-desc" v-html="productDescript"></div>
        `
    }
    usf.register(UsfCardDesc, null, "usf-card-desc");
 ```
