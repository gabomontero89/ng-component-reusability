# Component Class Mixins

We can also leverage a less-known method known as  *Mixins* to share logic between Angular component classes. Mixins allow 
us to compose multiple small classes that extend the target class but without having to use *multiple inheritance*.

## Example of Typescript Mixin
Let’s see what a mixin is with a simple example. First, we define a base class:
```
class BaseInput {
  label: string;
  disabled: boolean;
}
```
Next, we define a function that extends the base class with a new mini-class:
```
function themeMixin(BaseClass) {
    return class extends BaseClass {
      theme: string;
    }
}
```
...and finally, we extend the `BaseInput` class with the mixin:
```
class TextInput extends themeMixin(BaseInput) {}
```

## Let's build TheComponent using Mixins
```
export function classOneMixin(BaseClass) {  
    return class extends BaseClass {  
        page = 0;  
        itemsPerPage = 2;

        get start() { ... }

        get end() { ... }

        get pages() { ... }

        changePage(page: number) { ... }
    }
}
```
```
export function classTwoMixin(BaseClass) {  
    class ClassTwoComponentMixin extends BaseClass {  
        @Output() selected = new EventEmitter<Item>();  
        @Output() unselected = new EventEmitter<Item>();  

        selectedItems: Item[] = []; 
        select(item: Item) { ... }  

        unselect(item: Item) { ... }  

        isItemSelected(item: Item) { ... }  
    }  
    
  return ClassTwoComponentMixin;  
}
```
Once we define all the mixins we need to compose the component, we import the mixins and pass the Base class as an argument.

Then, we simply extend `TheComponent` with the mixin `TheComponentMixin`.
```
const TheComponentMixin = classTwoMixin(classOneMixin(BaseComponent));

@Component( ... )  
export class TheComponent extends TheComponentMixin {}
```
#

**While mixins also have several pitfalls, I do share the opinion that mixins are a more elegant and secure 
solution for multiple inheritance, at least in the long term.**
