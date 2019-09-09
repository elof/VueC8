# VueC8

# Intoduction

VueC8 is a small and pragmatic solution to create realtime bindings between a C8DB and your Vue application. Making it straightforward to always keep your local data in sync with remotes databases.

##Why
While [JSC8](https://github.com/macrometacorp/jsC8 'JSC8') does provide an API to keep your local data in sync with any changes happening in the remote database, it is more tedious. Here is the code you need to write to keep your local state in sync with C8DB without using Vuec8. Let's take the example of binding a collection.

To explore more you can visit [jsc8_tutorial](https://cdn.document360.io/d1a6730a-fd70-4f0a-a08d-dfa28ca8b958/Images/Documentation/pyc8_tutorial.png)

    Fabric = require('jsc8')
    fabric = new Fabric("MY-C8-URL");
    ...
    ...
    fabric.login(tenant, user, password)
              .then(() => {
              fabric.useTenant("TENANT-URL")
    }
    ...
    ...

    new Vue({
    	data: () => ({
    		todos: []
    	}),
    	created() {
    		fabric.query(`C8QL query`)
        		.then((cursor: ArrayCursor) => {
    				this.todo= cursor
    			});
    		const subcription = fabric.colleciton('Collection Name');

    		subscription.onchange({
    			onmessage:function onchnage(documentMsg){
    				// Write logic here to manage the updated document
    			}
    		},
    		URL);
    		},
    	//manage unsbscribe onDestroy.
    })

Now let's look at the equivalent code with vuec8:

    Vue.use(vuec8, {
            auth: {
              tenant: 'TENANT_NAME',
              password: 'PASSWORD',
              user: 'USERNAME',
            },
            config: 'URL OR CONFIG obj',
          })

    new Vue({
    	el: '#app',
    	data: {
    		todos: [],
    	},
    	c8db: {
    		todos: 'colleactionName',
    	},
    })

    ## Download and Install JavaScript Client

# Installation

In order to get started make sure to install the latest version of **vuec8**

```bash
yarn add vuec8
## - or -
npm install vuec8
```

Or from source

```bash
git clone https://github.com/macrometacorp/vuec8.git
cd VueC8
npm install
npm run build
```

## Plugin

VueC8 must be installed as a Vue plugin

    import { vuec8 } from "vuec8";

    Vue.use(vuec8, {
            auth: {
              tenant: 'TENANT_NAME',
              password: 'PASSWORD',
              user: 'USERNAME',
            },
            config: 'URL OR CONFIG obj',
          })

#API
**1. Plugin Options **

```json
{
  "auth": {
    "tenant": "TENANT_NAME",
    "password": "PASSWORD",
    "user": "USERNAME"
  },
  "fabricName": "fabrciname",
  "config": "url"
}
```

- **config** : string or string[] or [Config](https://github.com/Macrometacorp/jsC8/blob/master/src/connection.ts#L66 'Config')

**2. Vue component**
c8db is injected into your vue objec, you can assign c8db a object or a function which returns a object. object keys should be same as the keys defined in your vue data object and the values should be of Type

```
string | {
      collection: string;
      filter?: Array<{
					  fieldPath: string;
					  opStr: "<" | "<=" | "==" | ">=" | ">";
					  value: any;
					}>;
      documentId?: string;
      fabricName?: string;
    }
```

- **fabricName** If fabric name is not assigned it will use the fabricName assigned in Plugin options, if fabricName in plugin options is also not assigned it will take as default fabricName (\_system)

- **filter** will **AND** the conditions togather.
  eg: [{//condition 1},{ //condition 2}] : **FILTER condition 1 AND conditon 2**

- **documentId** if documentId is assigned it will fetch a specific document in a collection.

```javascript
new Vue{
...
	data:{
		todos:[],
	},
	c8db:{
		todos: "collection"
	},
...
}
```

or

````javascript
new Vue{
...
	data:{
		todos:[],
	},
	c8db:{
		todos: {
		fabricName: "fabric",
		collection : "collection",
		filter:[{
					fieldPath: "value",
					opStr: "<",
					value: 5
			}]
		}
	},
...
}
```

**3. c8Refs**
- vuec8 adds **c8Refs**  in every component where c8db is initialized. c8Refs will have refrence to you collection & fabric instance for the a every data key refrenced in c8db.
[jsc8](https://github.com/Macrometacorp/jsC8 "jsc8") has complete guide on [fabirc](https://github.com/Macrometacorp/jsC8/blob/master/docs/Reference/Database/FabricManipulation.mdhttp:// "fabirc") & [collection](https://github.com/Macrometacorp/jsC8/tree/master/docs/Reference/Collection "collection") instance.

```javascript
new Vue{
...
	data:{
		todos:[],
	},
	c8db:{
		todos: "collection"
	},
	updated(){
	  // It can be used to handle customized behavior.
		this.$c8Refs.todos._collection // collection refrence for todos
		this.$c8Refs.todos._fabric // fabric refrence for todos
	},
...
}
```
````