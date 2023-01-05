## Display lowest/highest variant price when using multiple filter

```javascript
 usf.event.add('sr_dataReceived', function(t, data){
        
        const facetFilter = !!usf.queryRewriter.facetFilters && !!usf.queryRewriter.facetFilters['950183103'] && usf.queryRewriter.facetFilters['950183103'][1].length > 1 ? usf.queryRewriter.facetFilters['950183103'][1] : false;

        data.items.forEach(item => {
            const selectedVariants = [];

            item.variants.forEach(v => {
                const v_size_option = item.options[0].values[v.options[0]];
                const isSelected = facetFilter.find(f => f == v_size_option);
                if (!!isSelected) selectedVariants.push(v);
            });

            if(selectedVariants.length === 1) return;

            const selectedV = usfLowestNumber(selectedVariants);

            item.selectedVariantId = selectedV.id
        })
    });
    
    function usfLowestNumber(arr) {
    const length = arr.length;
    let max = arr[0]
    for(let i = 1; i < length; i++) {
        if(max.price > arr[i].price) {
            max = arr[i];
        }
    }
    return max
}
```

## Swatch showing wrong picture

```javascript
this.product.options.map((option,optionIndex) => {
                    // check if it's the color option
                    var isColor = s.colorNames == option.name;
                    var renderedOptions = {};
                    var vid = 0;

                    if (!s.hideOptions.includes(option.name)) return h('ul', {
                        class: {
                            'usf-is-color': isColor,
                            'usf-swatch-circle': isColor && s.swatchType == 'circle'
                        },
                        attrs: { 'data-option-index': optionIndex }
                    }, [
                        option.values.map((o, index) => {
                            var vlength = this.product.variants.length;
                            for (let x = 0; x < vlength; x++) {
                                var v = this.product.variants[x];

                                // Prevent using the same variant again
                                if (vid === v.id) continue;
                                vid = v.id;
```
## Customer redirect filter
```html
<template v-if="_usfHasContainer">
        <template v-if="hasFilters">
            <usf-new-filters class="usf-sr-filters"></usf-new-filters>
        </template>
        <usf-new-sr></usf-new-sr>
    </template>
```

```javascript
// Begin custom redirect filter
    var NewSearchResult = {
        mixins: [usf.components.SearchResults],
        template: '',
    }
    usf.register(NewSearchResult, null, 'usf-new-sr');
    var NewFilter = {
        data() {
            return {
                facetCollection: '',
                facetStrength: '',
                selectedCollectionId: '',
                selectedStrength: '',
            }
        },
        mixins: [usf.components.Filters],
        template: `
            <div class="col-12">
                <div class="col-12 text-center">
                    <h2>Quick Find</h2>
                    <p>Select your preferences to shop a personalized collection of styles.</p>
                </div>
                <div class="col-12">
                    <div class="col-6">
                        <select id="usfCollection" @change="_usfFetchCollectionStrengthFilter(selectedCollectionId)" v-model="selectedCollectionId">
                            <option value="" selected>Any Gender</option>
                            <template v-for="f in facetCollection">
                                <option :key="f.id" :value="f.id" v-html="f.title"></option>
                            </template>
                        </select>
                    </div>
                    <div class="col-6">
                        <select id="usfStrength" v-model="selectedStrength">
                            <option value="" selected>Any Strength</option>
                            <template v-for="f in facetStrength.labels">
                                <option :key="f.id" :value="f.id" v-html="f.label"></option>
                            </template>
                        </select>
                        <div class="filter-button"> 
                            <usf-new-filter-option v-for="o in facetStrength.labels" :facet="facetStrength" :option="o" :key="o.label"></usf-new-filter-option>
                        </div>
                    </div>
                </div>
                <div>
                    <button @click="_usfRedirect()">
                        Find my glasses 
                        <span aria-hidden="true">›</span>
                    </button>
                </div>
            </div>
        `,
        created(){ 
            if(window._usfHasContainer){
                var t = this;
                t._usfFetchCollectionStrengthFilter();
                usf.queryRewriter.facetFilters = ''
            } 
             
        },
        methods: {
            _usfFetchCollectionStrengthFilter(collectionId = '') {
                var p = { 
                    q: '',
                    apiKey: usf.settings.siteId,
                    skip: 0,
                    take: 1, 
                    collection: collectionId,
                }
                var params = new URLSearchParams(p)
                var apiUrl = usf.settings.searchSvcUrl + 'search?' + params;
                var t = this;
                fetch(apiUrl).then(response => response.json()).then((data) => {
                    if (data.data && data.data.facets) {
                        if (!t.facetCollection) t.facetCollection = data.data.extra.collections;

                        var facetsResponse = data.data.facets;
                        var facetStrength = facetsResponse.find(f => f.title == 'Size');
                        if (facetStrength) {
                            t.facetStrength = facetStrength;
                        }
                    }
                })
            },
            _usfRedirect() {
                const COLLECTION_URL = 'https://usf-ui-test-1.myshopify.com/collections/'
                const collectionUrlName = this.selectedCollectionId ? this.facetCollection.find(c => c.id = this.selectedCollectionId).urlName : 'all'
                let searchParam = '';
                if (this.selectedStrength) {
                    this._usfSelectOption(this.selectedStrength);
                    searchParam = '?' + window.location.search.substring(1).split('&').find(p => p.includes('Size'))
                }
                
                const redirectURL = COLLECTION_URL + collectionUrlName + searchParam;
                return window.location.href = redirectURL;
            },
            _usfSelectOption(option) {
                if(!option) return;
                const el = this.$el;
                const selector = '#' + option;
                const optionEl = el.querySelector('.filter-button').querySelector(selector);
                optionEl.click();
            }
        },
    }
    usf.register(NewFilter, null, 'usf-new-filters');

    var NewFilterOption = {
        mixins: [usf.components.FilterOption],
        template: `
            <div :id="label" @click="onToggle" v-html="label" style="display: none;"></div>
        `,
    }
    usf.register(NewFilterOption, null, 'usf-new-filter-option');
    // End custom redirect filter
```
