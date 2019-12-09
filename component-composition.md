# Component Composition

Is a technique that complements *inheritance* and *mixins*: 

> Instead of extending a component with more functionality, we can 
> combine multiple smaller components to achieve the same result.

## Leveraging the Power of ngTemplateOutlet

The first component will be a generic, reusable component  `ListComponent`, its render the items based 
on start and end indexes as provided by the parent component.

The component doesn't dictate how to render each individual item, so: 

> We let the parent component define that by providing  `ngTemplateOutlet`  and
> passing each item as context.

```
@Component({  
  selector: "list",  
  template: `
    <div *ngFor="let item of items | slice : start : end">  
      <ng-container *ngTemplateOutlet="template; context: { item: item }">  
      </ng-container>
    </div>  
`  
})
export class ListComponent {  
    @Input() items: Item[] = [];  
    @Input() itemsPerPage = 2;  
    @Input() currentPage: number;

    @ContentChild('item', { static: false }) template: TemplateRef<any>; 

    get start() { ... } 
    get end() { ... }  
}
```

## The PageComponent

Then, we add the `PageComponent` component that takes care of listing the page numbers, and to 
notify the parent when the user clicks on a page:
```
@Component({  
  selector: "page",  
  template: `  
    <div class="pages">  
      <div  
       *ngFor="let p of pages; let i = index"  
       class="page"  
       [class.selected]="i === currentPage  
       (click)="pageChanged.emit(i)"  
      >{{ i }}</div>  
   </div>  
`  
})  
export class PageComponent {  
  @Input() currentPage: number;  
  @Input() itemsPerPage = 2;  
  @Input() itemsLength: number;  
    
  @Output() pageChanged = new EventEmitter<number>();  
    
  get pages() { ... }  
}
```

## BuyerComponent

Next, we define the `BuyerComponent` component to represent each item in the list, so: 

> It takes care of defining how the item is displayed, 
> and dispatch events when the item is selected/unselected:

```
@Component({  
  selector: "buyer",  
  template: `   
    <label>  
      <input  
        type="checkbox"  
        [checked]="isSelected"  
        (change)="$event.target.checked ? selected.emit(item) : unselected.emit(item)"  
      />  
      {{ item.display }}  
   </label>  
`  
})  
export class BuyerComponent {  
  @Input() item: Item;  
  @Input() isSelected: boolean;
  
  @Output() selected = new EventEmitter<Item>();  
  @Output() unselected = new EventEmitter<Item>();  
}
```

## BuyerListComponent

Now it's time to put all together! We can reuse the previously defined components to 
compose a list of customers.

> These components are all reusable and can be composed with any other list.

```
@Component({  
  selector: "composition-buyer-list",  
  template: `  
    <list  
     [items]="items"  
     [itemsPerPage]="2"  
     [currentPage]="currentPage"  
    >  
     <ng-template #item let-item="item">  
       <buyer 
        (selected)="selected($event)"  
        (unselected)="unselected($event)"  
        [item]="item"  
        [isSelected]="isItemSelected(item)"  
       ></buyer>
     </ng-template>  
    </list>  
   
    <page  
     [currentPage]="currentPage"  
     [itemsLength]="items.length"  
     [itemsPerPage]="2"  
     (pageChanged)="currentPage = $event"  
    ></page>  
`  
})  
export class CompositionBuyerListComponent {  
    @Input() items = [];  

    currentPage = 0;  
    selectedItems = [];  

    selected(item) { ... } 
    unselected(item) { ... } 
    isItemSelected(item) { ... }
}
```

#
**Component Composition is the ultimate way to create highly-reusable, clean, and effective components.**
