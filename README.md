# nodeTree

### Explanation

#### Use readFile function to find the attached csv file which contains 1000 row of a tree structure data.

HTML:

```html
<p>Select local CSV File:</p>
<input id="csv" type="file">
```

JavaScript:

```javascript
var fileInput = document.getElementById("csv");
var finalResult = [];
function readFile() {
  let reader = new FileReader();
  reader.onload = function () {
    console.log(reader.result);
    initApp(reader.result);
  }
  // start reading the file. When it is done, calls the onload event defined above.
  reader.readAsBinaryString(fileInput.files[0]);
};
fileInput.addEventListener('change', readFile);
```



<u>**The result can be triggered by initApp function and the results will be display in *console*. I run the function by a few lines of code that**</u> 

```javascript
// remove(root, '563');
add(root, new Node('1111111', '1111111', '1', '100'));
```

These lines of codes mean that removing the node whose id is 563 and add new node ('1111111', '1111111', '1', '100') into his parent node whose id is equal to 1. 

```javascript
add(root, new Node('1111111', '1111111', '1', '100'));
```

Result:

The new node will be added into his parent node whose id is equal to 1. 

![add_node](/Users/chenmin/Desktop/nodeTree/add_node.png)

![add_node_2](/Users/chenmin/Desktop/nodeTree/add_node_2.png)

Now the new node has been inserted into root node.



```javascript
remove(root, '563');
```

Result:

node whose id is equal to 536 is that {563,Brentwood,1,4.92}. We need to remove the node from its parent node.

![remove_node](/Users/chenmin/Desktop/nodeTree/remove_node.png)



![remove_node_2](/Users/chenmin/Desktop/nodeTree/remove_node_2.png)

The total amount of root node that is equal 50881.18 substracts the total amount of node (Brentwood), then the result will be 50853.75.

**Full trigger function:**

````javascript
function initApp(fileContent) {
  let results = fileContent.split("\n").slice(1);
  let tree = createTree(results);
  let root = createTree(results)['1'];
  traverse(root, function (node) {
    finalResult.unshift(node.name + " amount: " + node.total + '\n');
  })
  console.log(finalResult.join(""));
  /*=============================*/
  // remove(root, '563');
  add(root, new Node('1111111', '1111111', '1', '100'));
  traverse(root, function (node) {
    finalResult.unshift(node.name + " amount: " + node.total + '\n');
  })
  console.log(finalResult.join(""));
}
````

​

#### This code block is mainly to create node, tree and traverse tree. 

```javascript
 
  //create node
  function Node(id, name, parentId, amount) {
    this.id = id;
    this.name = name;
    this.parentId = parentId;
    this.amount = parseFloat(amount);
    this.children = [];
  }

  //create tree
  function createTree(results) {
    let tree = {};
    for (let i = 0; i < results.length; i++) {
      if (results[i].length > 0) {
        const elementArr = results[i].split(",");
        let id = elementArr[0];
        let name = elementArr[1];
        let parentId = elementArr[2];
        let amount = elementArr[3];
        
        //tree[id] is used to identify whether the node exist or not. if the node exists, just      		assign id, name, parentId and amount to this node
        if (tree[id]) {
          tree[id].id = id;
          tree[id].name = name;
          tree[id].parentId = parentId;
          tree[id].amount = amount;
        } else {
          tree[id] = new Node(id, name, parentId, amount);
        }
        // if parentId is not equal to id, it will continue to check whether his parent node 			exists or not. tree[parentId] can be its parent node, then put current node into his 			parent node.
        if (parentId != id) {
          if (tree[parentId]) {
            tree[parentId].children.push(tree[id]);
          } else {
            //if parent node doesn't exist, directly create a empty object and put the current 				node into its children array. when the if statement above identifies that this node 			exists, just need to assign the id, name, parentId and amount into node.
            tree[parentId] = {};
            tree[parentId].children = [tree[id]];
          }
        }
      }
    }
    return tree;
  }

//use DFS to search every node and caculate the amount
  function traverse(node, callback) {
    node.total = node.amount;
    for (let index = 0; index < node.children.length; index++) {
      const element = node.children[index];
      node.total += traverse(element, callback);
    }
    callback(node);
    return node.total;
  }
```

​

#### This code block is to allow user to add or remove a tree node.

```javascript
function remove(root, idToRemove) {
  let nodeToRemove = null;
  let parentNode = null;
  //start from the root node
  //use DFS
  //if idToRemove is equal to node.id, it shows that the node that shoulde be removed has been 		found.
  traverse(root, function (node) {
    if (node.id == idToRemove) {
      nodeToRemove = node;
      return;
    }
  });
  if (!nodeToRemove) {
    throw new Error('Node does not exist.');
    return;
  }
  //the parent node can be found by removed node
  traverse(root, function (node) {
    if (node.id == nodeToRemove.parentId) {
      parentNode = node;
      return;
    }
  });
  if (!parentNode) {
    throw new Error('Parent does not exist.');
    return;
  }
  //use findIndexById function to delete the removed node in children array of parent node.
  if (parentNode) {
    let index = findIndexById(parentNode.children, idToRemove);
    if (index === undefined) {
      throw new Error('Node to remove does not exist.');
    } else {
      parentNode.children.splice(index, 1);
    }
  }
  return nodeToRemove;
}
//find the id 
function findIndexById(arr, id) {
  let index;
  for (let i = 0; i < arr.length; i++) {
    if (arr[i].id === id) {
      index = i;
    }
  }
  return index;
}
//add new node in the toNode
function add(root, child) {
  let parentNode = null;
  traverse(root, function (node) {
    if (node.id == child.parentId) {
      parentNode = node;
      return;
    }
  });
  if (parentNode) {
    parentNode.children.push(child);
  } else {
    throw new Error('Cannot add node to a non-existent parent.');
  }
}
```

