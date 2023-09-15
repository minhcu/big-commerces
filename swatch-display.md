```html
<!-- List + Swatch filter -->
<div v-else ref="values" :class="'usf-facet-values usf-scrollbar usf-facet-values--' + (facet.title == 'Color' ? 'List' : facet.display) + (facet.navigationCollections ? ' usf-navigation' : '') + (facet.valuesTransformation ? (' usf-' + facet.valuesTransformation.toLowerCase()) : '') + (facet.circleSwatch ? ' usf-facet-values--circle' : '')" :style="!usf.isMobileFilter && facet.maxHeight ? { maxHeight: facet.maxHeight } : null">
    <!-- Filter options -->                
    <usf-filter-option v-for="o in visibleOptions" :facet="facet" :option="o" :key="o.label"></usf-filter-option>
</div>


<button v-else :data-title="facet.title" :class="(isSelected ? 'usf-selected ' : '') + (swatchImage ? (facet.title == 'Color' ? '' : ' usf-facet-value--with-background') : '') + ' usf-btn usf-relative usf-facet-value usf-facet-value-' + (facet.multiple ? 'multiple' : 'single')" :title="isSwatch || isBox ? label + ' (' + option.value + ')' : undefined" :style="usf.isMobileFilter ? null : (facet.title == 'Color' ? null : swatchStyle)" @click.prevent.stop="onToggle">
    <!-- checkbox -->
    <div v-if="!isBox && !isSwatch && facet.multiple" :class="'usf-checkbox' + (isSelected ? ' usf-checked' : '')">
        <span class="usf-checkbox-inner"></span>
    </div> 

    <!-- swatch image in mobile -->
    <div v-if="swatchImage && usf.isMobileFilter || facet.title == 'Color'" class="usf-mobile-swatch" :style="swatchStyle"></div>

    <!-- option label -->
    <span class="usf-label usf-btn" v-html="label"></span>
    
    <!-- product count -->
    <span v-if="!(!usf.settings.filterNavigation.showProductCount || (swatchImage && !usf.isMobileFilter)) && option.value !== undefined" class="usf-value">{{option.value}}</span>
</button>
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
