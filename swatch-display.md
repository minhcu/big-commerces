```html
<!-- List + Swatch filter -->
<div v-else ref="values" :class="'usf-facet-values usf-scrollbar usf-facet-values--' + (facet.title == 'Color' ? 'List' : facet.display) + (facet.navigationCollections ? ' usf-navigation' : '') + (facet.valuesTransformation ? (' usf-' + facet.valuesTransformation.toLowerCase()) : '') + (facet.circleSwatch ? ' usf-facet-values--circle' : '')" :style="!usf.isMobileFilter && facet.maxHeight ? { maxHeight: facet.maxHeight } : null">
    <!-- Filter options -->                
    <usf-filter-option v-for="o in visibleOptions" :facet="facet" :option="o" :key="o.label"></usf-filter-option>
</div>


<!-- swatch image in mobile -->
<div v-if="swatchImage && usf.isMobileFilter || facet.title == 'Color'" class="usf-mobile-swatch" :style="swatchStyle"></div>
```

```css
.usf-facet-values--List .usf-facet-value[data-title="Color"] {
    display: flex;
    align-items: center; 
}
.usf-facet-values--circle [data-title="Color"] .usf-mobile-swatch {
    border-radius: 9999px;
}
.usf-facet-values--List .usf-facet-value.usf-selected[data-title="Color"] .usf-mobile-swatch {
    border: 2px solid #000;
}
```
