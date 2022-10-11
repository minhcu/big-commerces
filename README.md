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
// Put this in init function
usf.settings.priceFormat = reverseDisplayPrice(usf.settings.priceFormat);

function reverseDisplayPrice(price) {
    var arr = price.split(' ');
    var temp = arr[0];
    arr[0] = arr[1];
    arr[1] = temp;
    return arr.join('');
}
```

## Continue Selling

Pre-order ON > variant.flags = 1;


Remodify search-results_items.js
```javascript

const isPreorder = !!selectedVariantForPrice.flags

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
                        return this.productDescript = r.data.description.replace(/\\n/g, "<br>");
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
 
 ## Theme settings
 Normally, we can use `_usfThemeSettings`, but it isn't available in preview mode. Therefore we will declare a temporary variable in the theme init.
 
 Used in theme:
 - Bespoke
 
 ```javascript
 function _usfSetDefaultThemeSettings() {
    window._usfNoneSalePriceLabel = _usfThemeSettings['non-sale-price-label'] || 'Was:'
    window._usfSalePriceLabel = _usfThemeSettings['sale-price-label'] || 'Now:'
 }
 ```
 
 ## Rating
 
 Used in theme:
 - Artisan
 - Beautify
 
 ```javascript
 var UsfRating = {
        props: {
            product: Object
        },
        data() {
            var product = this.product;
            var hasRating = product.reviewCount > 0;
            var rating;

            if (hasRating) rating = Math.floor(product.review / product.reviewCount);

            return {
                rating: rating,
            };
        },
        template:`
            <p v-if="!!rating" class="card-text" data-test-info-type="productRating">
                <span class="rating--small">
                    <span v-for="i in 5" :key="i" :class="'icon icon--rating' + (rating - i >= 0 ? 'Full' : 'Empty')">
                        <svg>
                            <use xlink:href="#icon-star"></use>
                        </svg>
                    </span>
                </span>
            </p>
        `
    }

    usf.register(UsfRating, null, "usf-rating");
 ```
