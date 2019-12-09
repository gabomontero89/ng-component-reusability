# Component Class Inheritance

The most used way to share code among Angular components, is ES6 *class inheritance* using the *extends* keyword. When used in the right way and is not abused, the technique is a decent solution for sharing code between components.

A common way of using this technique is to create an abstract class and define there the methods shared by all the subclasses. A subclass may have to implement the abstract methods or override the existing ones.

**Component Base class**

The Base class is very simple: we simply define the Input items.

    export abstract class BaseComponent {
	    @Input() items: Item[];
    }

Next, we proceed and extend `BaseComponent` with two more abstract classes.

**ClassOneComponent**

The component `ClassOneComponent` extends `BaseComponent` and adds functionality.
```
export abstract class ClassOneComponent extends BaseComponent {
    page = 0;  
    itemsPerPage = 2;

    get start() { ... }

    get end() { ... }

    get pages() { ... }

    changePage(page: number) { ... }
}
```
**ClassTwoComponent**

The component `ClassTwoComponent` extends `ClassOneComponent` and adds functionality too.
```
export abstract class ClassTwoComponent extends ClassOneComponent {
    @Output() selected = new EventEmitter<Item>();
    @Output() unselected = new EventEmitter<Item>();

    selectedItems: Item[] = []; 
    select(item: Item) { ... }  

    unselect(item: Item) { ... }  

    isItemSelected(item: Item) { ... }
}
```
**Implementation component: TheComponent**

Finally, we create an implementation of the class `TheComponent` and extend it `ClassTwoComponent`. 
The template and the component will have access to all the outputs and inputs we specified in the other classes.
```
@Component({
  selector: 'the-component',
  template: `
    <div *ngFor="let item of items | slice: start : end">
      <label>
        <input
          type="checkbox"
          [checked]="isItemSelected(item)"
          (change)="$event.target.checked ? select(item) : unselect(item)"
        />
        {{ item.display }}
      </label>
    </div>
    <div class='pages'>
      <div *ngFor="let p of pages; let i = index;"
        class='page'
        [class.selected]="i === page"
        (click)="changePage(i)"
      >{{ i }}</div>
    </div>
  `
})
export class TheComponent extends ClassTwoComponent {}
```
