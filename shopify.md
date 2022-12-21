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
