```javascript

/* Begin theme init code */
/*inc_begin_theme-init*/
var ut = usf.utils.send;
if (ut)
    usf.utils.send = (data, dontInvokePlugins) => {
        try {
            // let newData = data;
            data['data'] = data['data'] || {}; 
            let q = data['data']['q'] || "";
            if (data['url'] && data['url'].includes('instantsearch')) {
                delete data['sort']
            }

        } catch (e) {
            console.log("Errrorr : ", e)
        }
        return ut(data, dontInvokePlugins)
    }
/*inc_end_theme-init*/

usf.event.add('preinit', function () {
    var defaultSort = 'manual';
    if (defaultSort && !usf.platform.isCollectionPage) {
        console.log('Collection default sort: ', defaultSort)
        var sortOrder = {
            'manual': 'r',
            'best-selling': 'bestselling',
            'title-ascending': 'title',
            'title-descending': '-title',
            'created-ascending': 'date',
            'created-descending': '-date',
            'price-ascending': 'price',
            'price-descending': '-price',
        }[defaultSort];

        if (sortOrder){
            var sortFields = usf.settings.search.sortFields;
            if (!sortFields || !sortFields.length || sortOrder !== sortFields[0])
                usf.queryRewriter.sort = sortOrder;
        }
    }
})

/* End theme init code */

```
