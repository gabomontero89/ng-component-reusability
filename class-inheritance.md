# Component Class Inheritance

The most used way to share code among Angular components, is ES6 *class inheritance* using the *extends* keyword. When used in the right way and is not abused, the technique is a decent solution for sharing code between components.

A common way of using this technique is to create an abstract class and define there the methods shared by all the subclasses. A subclass may have to implement the abstract methods or override the existing ones.

**ListComponent as Base class**

The Base class is very simple: we simply define the Input items.

    export abstract class ListComponent {
	    @Input() items: Item[];
    }

Next, we proceed and extend `ListComponent` with two more abstract classes.

**PageComponent**

The component `PageComponent` extends `ListComponent` and adds functionality.
```
export abstract class PageComponent extends ListComponent {
    page = 0;  
    itemsPerPage = 2;

    get start() { ... }

    get end() { ... }

    get pages() { ... }

    changePage(page: number) { ... }
}
```
**SelectListComponent**

The component `SelectListComponent` extends `PageComponent` and adds functionality too.
```
export abstract class SelectListComponent extends PageComponent {
    @Output() selected = new EventEmitter<Item>();
    @Output() unselected = new EventEmitter<Item>();

    selectedItems: Item[] = []; 
    select(item: Item) { ... }  

    unselect(item: Item) { ... }  

    isItemSelected(item: Item) { ... }
}
```
**Implementation component: BuyerListComponent**

Finally, we create an implementation of the class `BuyerListComponent` and extend it `SelectListComponent`. 
The template and the component will have access to all the outputs and inputs we specified in the other classes.
```
@Component({
  selector: 'buyer-list',
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
export class BuyerListComponent extends SelectListComponent {}
```
