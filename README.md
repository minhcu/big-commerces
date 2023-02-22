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

## Custom swatch 2 - need API

```javascript
function getProductOptionsv2(id, name, url) {
  var pv_url = "https://sportif.projectahost.com/api/productoptions/av/" + id;
  $.ajax({
    type: "GET",
    url: pv_url,
    // The key needs to match your method's input parameter (case-sensitive).
    contentType: "application/json; charset=utf-8",
    dataType: "json",
    success: function (data) {
      var options = "";
      var attr_container = "";
      var pa_product_name = name;
      $.each(data, function (variant_idx, variant) {
        if (variant.inventory > 0) {
          if (variant.swatch_image != "") {
            options +=
              '<a class="card-swatch card-swatch-color" href="' + url + "?c=" + variant.color + '" data-imageurl = "' + variant.image_url + "\" onmouseover='swap_swatch_in(" + id + ',event)\'><span class="card-swatch-image" title="' + variant.color + '"><img src="' + variant.swatch_image + '" data-imageurl = "' + variant.image_url + '" ></span></a>';
          } else {
            options +=
              '<a class="card-swatch card-swatch-color" href="' +
              url +
              "?c=" +
              variant.color +
              '" data-imageurl = "' +
              variant.image_url +
              "\" onmouseover='swap_swatch_in(" +
              id +
              ',event)\'><span class="card-swatch-image" title="' +
              variant.color +
              '" data-imageurl = "' +
              variant.image_url +
              '" >' +
              variant.color +
              "</span></a>";
          }
        } else {
          /*
                    if(variant.swatch_image != '')
                    {
    				    options += '<a class="card-swatch card-swatch-color unavailable"  data-imageurl = "' + variant.image_url + '" ><span class="card-swatch-image" title="' + variant.color + '"><img src="' + variant.swatch_image + '"></span></a>';                                             
                    }
                    else{
                        options += '<a class="card-swatch card-swatch-color unavailable"  data-imageurl = "' + variant.image_url + '" ><span class="card-swatch-image" title="' + variant.color + '">' + variant.color + '</span></a>';                                             
                    }
                    */
        }
      });
      $("#pa-options-container-" + id).html(options);

      /*
        $.each(data.option_values, function(idx, option){            
             $("#pa-option-container-{{id}}").append('<button type="button" onclick="card_add_to_cart({{id}},' + option.id + ',' + option.option_id + ')">' + option.label + '</button>');
           });
           */
    },
    error: function (errMsg) {
      //alert("error: " + errMsg);
    },
  });
}

function loadProductOption(id, name, url) {
  getProductOptionsv2(id, name, url);
}

function swap_swatch_in(id, e) {
  var img = e.target;
  img_url = $(img).attr("data-imageurl");
  if (img_url != "") {
    $("#card_" + id + "_image").attr("srcset", "");
    $("#card_" + id + "_image").attr("src", img_url);
  }
}
```

## Custom product description (due to product api return null)

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
                    return this.productDescript = r.data.description.replace(/\\n/g, "");
                },
            });
        }
        else {
            this.productDescript = this.product.description;
        }
    },
    template: `
        <p v-html="_usfTruncateWords(productDescript,40,'...')"></p>
    `
}
usf.register(UsfCardDesc, null, "usf-card-desc");
 ```
 
 ## Theme settings
 
 ```javascript
 _usfThemeSettings['non-sale-price-label'] || _usfThemeSettings['pdp-non-sale-price-label']
 _usfThemeSettings['sale-price-label'] || _usfThemeSettings['pdp-sale-price-label']
 _usfThemeSettings['sale-badges'] || _usfThemeSettings.product_sale_badges_visibility
 _usfThemeSettings.product_sale_badges !== 'none'
 
 ```
 
 ## Rating
 
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
 
 ## Ajax add to cart
 
 Add this to addToCart/Pre-order button
 
 ```javascript
 @click="_usfAddToCartAjax"
 ```
 
 
 ```javascript
 class OverlayUtils {
  constructor() {
    this.body = document.body;
    this.overlay = document.querySelector('.loading-overlay');
  }

  show() {
    if(!this.body.classList.contains('scroll-locked')) this.body.classList.add('scroll-locked');
    if(!this.overlay.classList.contains('visible')) this.overlay.classList.add('visible');
  }

  hide() {
    this.body.classList.remove('scroll-locked');
    this.overlay.classList.remove('visible');
  }
}

function handleBtn(button) {
    if (!button.dataset.defaultText) {
        button.setAttribute('data-default-text', button.querySelector('.button-text').innerText)
        var progressText = button.dataset.progressText || '';

        button.classList.add("progress");
        button.setAttribute('disabled', 'disabled');
        button.querySelector('.button-text').innerText = progressText;
        return;
    }
    var defaultText = button.dataset.defaultText;

    button.classList.remove("progress");
    button.removeAttribute('data-default-text');
    button.setAttribute('disabled', false);
    button.querySelector('.button-text').innerText = defaultText;
}

window._usfAddCartAjax = function (e) {
    e.preventDefault();
    
    // Do not do AJAX if browser doesn't support FormData. No IE9.

    var overlay = new OverlayUtils;
    overlay.show();

    // Turn off quick add || or using ajax not supported browser
    if (_usfThemeSettings['product-purchase-disable-ajax'] || window.FormData === undefined) {
        window.location = addBtn.href
    }

    // Get <a> button (e.target return span)
    var addBtn = e.target.parentNode;
    var productTitle = addBtn.dataset.productTitle
    var productId = addBtn.dataset.productId
    var quantity = 1; // quick add 1 product only

    handleBtn(addBtn);

    // Invalid product
    if (!productId) { 
        handleBtn(addBtn);
        return overlay.hide();
    }

    var ultis = stencilUtils; //BIGCOMMERCE ultis

    var form = new FormData;
    form.append('action', 'add');
    form.append('product_id', productId);
    form.append('qty', quantity);

    // BIGCOMMERCE ultis
    ultis.api.cart.itemAdd(form, (err, response) => {
        let message;

        if (err || response.data.error) {
          message = err || response.data.error;
        } else {
          message = productTitle + 'added to cart!';
        }

        // document.body.dispatchEvent('quick-cart-refresh');

        setTimeout(() => {
            alert(message);
            handleBtn(addBtn);
        }, 500);
    });
}
 ```

## Image slider

```javascript
    var UsfSlider = {
        props: {
            product: Object,
            selectedImage: Object,
        },
        data() {
            let product = this.product;
            let images = this.product.images;
            let active = 0;
            return {
                product: product,
                images: images,
                active: active,
            };
        },
        computed: {
            activeImageIndex() {
                const index = this.active % this.images.length
                return Math.abs(index);
            }
        },
        template:`
            <div class="card-img-container card-img-container--slider">
                <img
                v-for="(img, i) in this.images"
                class="card-image"
                :class="(_usfThemeSettings.lazyload_mode != 'disabled' ? 'lazyload' : '') + (i === activeImageIndex ? ' is-active' : '' )"
                :data-src="img.url"
                data-sizes="auto"
                :alt="selectedImage.alt || product.title"
                :title="product.title"
                />
                <template v-if="this.images.length > 1">
                    <span v-if="activeImageIndex > 0" class="card-image-prev" @click.prevent="active--">
                        <span class="is-srOnly">Previous</span>
                        <i class="icon" aria-hidden="true"><svg><use xlink:href="#icon-chevron-left"></use></svg></i>
                    </span>
                    <span v-if="activeImageIndex < this.images.length - 1" class="card-image-next" @click.prevent="active++">
                        <span class="is-srOnly">Next</span>
                        <i class="icon" aria-hidden="true"><svg><use xlink:href="#icon-chevron-right"></use></svg></i>
                    </span>
                </template>
            </div>
        `
    };
    usf.register(UsfSlider, null, "usf-slider");
```

## multiple price custom

```javascript
var _usfPriceCustom = `
    <div v-if="loaded" class="usf-price-wrapper">
        <template v-if="!!data.price.non_sale_price_with_tax">
            <span class="usf-price usf-has-discount" v-html="data.price.non_sale_price_with_tax.formatted"></span>
            <span class="usf-discount" v-html="data.price.sale_price_with_tax.formatted"></span>
        </template>
        <template v-else>
            <span class="usf-price" v-html="data.price.with_tax.formatted"></span>
        </template>
    </div>
`;

var _usfPrice = {
        template: _usfPriceCustom,
        props: {
            product: Object,
        },
        data() {
            return {
                loaded: false,
                data: Object,
            };
        },
        created() {
            var self = this;
            stencilUtils.api.productAttributes.optionChange(this.product.id, {}, {}, (err, response) => {
                if (!err) {
                    self.loaded = true;
                    self.data = response.data;
                    console.log(self.data)
                    console.log(self.loaded);
                    }
                }
            );
        },
    };
    usf.register(_usfPrice, null, "usf-price-custom");
```

## judgeme review plugin

```javascript
var _usfJudgeMe = {
        props: {
            product: Object,
        },
        data() {
            return {
                loaded: false,
                data: Object,
            };
        },
        mounted() {
            var el = this.$el;
            // jdgm.SHOP_DOMAIN
            var URL = 'https://judge.me/api/v1/widgets/preview_badge'
            // jdgm.PUBLIC_TOKEN
            var TOKEN = '?api_token=nvAObxMqlzwB1_QJvr2-HFYG0X0'
            // jdgm.SHOP_DOMAIN
            var store = '&shop_domain=store-kuqmqgarn1.mybigcommerce.com'
            // Product URL only
            var handle = '&handle=' + this.product.urlName.split('/')[2]
            var fetchURL = URL + TOKEN + store + handle
            
            fetch(fetchURL)
            .then(rs => rs.json())
            .then(rs => this.data = rs)
        },
        template: `
            <div class="jdgm-widget jdgm-preview-badge jdgm-preview-badge--with-link jdgm--done-setup" style="text-align:center;" v-html="this.data.badge"><div>
        `
    };
    usf.register(_usfJudgeMe, null, "usf-judgeme");
```

## Yotpo plugin
```javascript
var _usfYotpo = {
        props: {
            product: Object,
        },
        template: `
            <div class="yotpo bottomLine" :data-product-id="this.product.id"><div>
        `
    }; 
    usf.register(_usfYotpo, null, "usf-yotpo");
usf.event.add(['sr_updated', 'sr_viewChanged', 'rerender'], function () {
    if (window.yotpo){
        try
        {
            yotpo.initWidgets()
        }
        catch (e){
            console.error('[USF] Failed to initialize Yotpo plugin. ' + e);
        }
    }
});
```
