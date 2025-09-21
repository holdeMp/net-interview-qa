# Angular Pipes Interview Questions & Answers 🔧

> Comprehensive collection of Angular pipes interview questions and answers for frontend developers. Covering built-in pipes, custom pipes, performance considerations, and best practices.

## 📋 Table of Contents

- [Pipes Overview](#pipes-overview)
- [Built-in Pipes](#built-in-pipes)
- [Custom Pipes](#custom-pipes)
- [Pipe Chaining and Parameters](#pipe-chaining-and-parameters)
- [Performance Considerations](#performance-considerations)
- [Best Practices](#best-practices)
- [Common Interview Questions](#common-interview-questions)

---

## 🔧 Pipes Overview

### Q1: What are Angular pipes and why are they important?

**Answer:**
Angular pipes are a powerful feature used to transform data in Angular templates. They allow you to format and manipulate data directly within the HTML, keeping the component class clean and focused on business logic rather than presentation.

**Key Benefits:**
- **Data Transformation**: Format and manipulate data in templates
- **Code Separation**: Keep presentation logic separate from business logic
- **Reusability**: Use pipes across multiple components
- **Performance**: Optimized change detection for pure pipes
- **Readability**: Clean and declarative template syntax

**Basic Syntax:**
```typescript
{{ data_expression | pipe_name : arg1 : arg2 : ... }}
```

**Components:**
- **data_expression**: The data you want to transform
- **pipe_name**: The name of the pipe you want to use
- **arg1, arg2, ...**: Optional arguments that can be passed to the pipe

**Example:**
```html
<!-- Assuming today is 23rd June, 2024 -->
<p>{{ today | date }}</p>
<!-- Output: Jun 23, 2024 -->
```

---

## 🛠️ Built-in Pipes

### Q2: What are the most commonly used built-in pipes in Angular?

**Answer:**
Angular provides several built-in pipes for common data transformation tasks:

**1. DatePipe - Date Formatting:**

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-date-example',
  template: `
    <div>
      <p>Default: {{ today | date }}</p>
      <p>Full Date: {{ today | date:'fullDate' }}</p>
      <p>Short Date: {{ today | date:'shortDate' }}</p>
      <p>Custom Format: {{ today | date:'dd/MM/yyyy' }}</p>
      <p>With Time: {{ today | date:'medium' }}</p>
    </div>
  `
})
export class DateExampleComponent {
  today = new Date();
}
```

**Output:**
```
Default: Jun 23, 2024
Full Date: Sunday, June 23, 2024
Short Date: 6/23/24
Custom Format: 23/06/2024
With Time: Jun 23, 2024, 10:30:45 AM
```

**2. UpperCasePipe / LowerCasePipe - Text Transformation:**

```typescript
@Component({
  template: `
    <div>
      <p>Original: {{ text }}</p>
      <p>Uppercase: {{ text | uppercase }}</p>
      <p>Lowercase: {{ text | lowercase }}</p>
      <p>Title Case: {{ text | titlecase }}</p>
    </div>
  `
})
export class TextExampleComponent {
  text = 'Hello World';
}
```

**Output:**
```
Original: Hello World
Uppercase: HELLO WORLD
Lowercase: hello world
Title Case: Hello World
```

**3. CurrencyPipe - Currency Formatting:**

```typescript
@Component({
  template: `
    <div>
      <p>USD: {{ price | currency:'USD':'symbol':'1.2-2' }}</p>
      <p>EUR: {{ price | currency:'EUR':'symbol':'1.2-2' }}</p>
      <p>GBP: {{ price | currency:'GBP':'symbol':'1.2-2' }}</p>
      <p>Code: {{ price | currency:'USD':'code':'1.2-2' }}</p>
    </div>
  `
})
export class CurrencyExampleComponent {
  price = 123.456;
}
```

**Output:**
```
USD: $123.46
EUR: €123.46
GBP: £123.46
Code: USD123.46
```

**4. DecimalPipe - Number Formatting:**

```typescript
@Component({
  template: `
    <div>
      <p>Default: {{ pi | number }}</p>
      <p>2 Decimals: {{ pi | number:'1.2-2' }}</p>
      <p>4 Decimals: {{ pi | number:'1.4-4' }}</p>
      <p>Percent: {{ percentage | percent:'1.2-2' }}</p>
    </div>
  `
})
export class NumberExampleComponent {
  pi = 3.14159265359;
  percentage = 0.75;
}
```

**Output:**
```
Default: 3.142
2 Decimals: 3.14
4 Decimals: 3.1416
Percent: 75.00%
```

**5. AsyncPipe - Asynchronous Operations:**

```typescript
import { Component, OnInit } from '@angular/core';
import { Observable, interval } from 'rxjs';
import { map } from 'rxjs/operators';

@Component({
  template: `
    <div>
      <p>Timer: {{ timer$ | async }}</p>
      <p>User Data: {{ user$ | async | json }}</p>
      <p>Loading: {{ isLoading$ | async ? 'Yes' : 'No' }}</p>
    </div>
  `
})
export class AsyncExampleComponent implements OnInit {
  timer$: Observable<number>;
  user$: Observable<any>;
  isLoading$: Observable<boolean>;

  ngOnInit() {
    this.timer$ = interval(1000);
    this.user$ = this.getUserData();
    this.isLoading$ = this.getLoadingState();
  }

  private getUserData(): Observable<any> {
    return new Observable(observer => {
      setTimeout(() => {
        observer.next({ name: 'John', age: 30 });
        observer.complete();
      }, 2000);
    });
  }

  private getLoadingState(): Observable<boolean> {
    return new Observable(observer => {
      observer.next(true);
      setTimeout(() => observer.next(false), 3000);
    });
  }
}
```

**6. JsonPipe - JSON Formatting:**

```typescript
@Component({
  template: `
    <div>
      <pre>{{ user | json }}</pre>
      <pre>{{ config | json }}</pre>
    </div>
  `
})
export class JsonExampleComponent {
  user = { name: 'John', age: 30, city: 'New York' };
  config = { theme: 'dark', language: 'en', notifications: true };
}
```

**7. SlicePipe - Array/String Slicing:**

```typescript
@Component({
  template: `
    <div>
      <p>First 3 items: {{ items | slice:0:3 }}</p>
      <p>Last 2 items: {{ items | slice:-2 }}</p>
      <p>String slice: {{ text | slice:0:10 }}</p>
    </div>
  `
})
export class SliceExampleComponent {
  items = ['Apple', 'Banana', 'Cherry', 'Date', 'Elderberry'];
  text = 'Hello World';
}
```

**8. KeyValuePipe - Object Iteration:**

```typescript
@Component({
  template: `
    <div>
      <div *ngFor="let item of user | keyvalue">
        <strong>{{ item.key }}:</strong> {{ item.value }}
      </div>
    </div>
  `
})
export class KeyValueExampleComponent {
  user = { name: 'John', age: 30, city: 'New York' };
}
```

---

## 🔨 Custom Pipes

### Q3: How do you create custom pipes in Angular?

**Answer:**
Custom pipes allow you to create reusable data transformations specific to your application needs.

**1. Basic Custom Pipe:**

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'reverse'
})
export class ReversePipe implements PipeTransform {
  transform(value: string): string {
    if (!value) return '';
    return value.split('').reverse().join('');
  }
}
```

**Usage:**
```html
<p>{{ 'Hello World' | reverse }}</p>
<!-- Output: dlroW olleH -->
```

**2. Parameterized Custom Pipe:**

```typescript
@Pipe({
  name: 'truncate'
})
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 10, suffix: string = '...'): string {
    if (!value) return '';
    if (value.length <= limit) return value;
    return value.substring(0, limit) + suffix;
  }
}
```

**Usage:**
```html
<p>{{ 'This is a long text' | truncate:10:'...' }}</p>
<!-- Output: This is a ... -->
```

**3. Advanced Custom Pipe with Multiple Parameters:**

```typescript
@Pipe({
  name: 'filter'
})
export class FilterPipe implements PipeTransform {
  transform(items: any[], searchText: string, property: string = 'name'): any[] {
    if (!items) return [];
    if (!searchText) return items;
    
    searchText = searchText.toLowerCase();
    return items.filter(item => 
      item[property].toLowerCase().includes(searchText)
    );
  }
}
```

**Usage:**
```html
<input [(ngModel)]="searchText" placeholder="Search users">
<div *ngFor="let user of users | filter:searchText:'name'">
  {{ user.name }}
</div>
```

**4. Impure Pipe (Runs on Every Change Detection):**

```typescript
@Pipe({
  name: 'random',
  pure: false // Impure pipe
})
export class RandomPipe implements PipeTransform {
  transform(value: any): number {
    return Math.random();
  }
}
```

**5. Pipe with Dependency Injection:**

```typescript
import { Pipe, PipeTransform, Inject, LOCALE_ID } from '@angular/core';

@Pipe({
  name: 'localizedCurrency'
})
export class LocalizedCurrencyPipe implements PipeTransform {
  constructor(@Inject(LOCALE_ID) private locale: string) {}

  transform(value: number, currencyCode: string = 'USD'): string {
    return new Intl.NumberFormat(this.locale, {
      style: 'currency',
      currency: currencyCode
    }).format(value);
  }
}
```

**6. Async Custom Pipe:**

```typescript
@Pipe({
  name: 'asyncFilter'
})
export class AsyncFilterPipe implements PipeTransform {
  transform(items: Observable<any[]>, filterFn: (item: any) => boolean): Observable<any[]> {
    return items.pipe(
      map(items => items.filter(filterFn))
    );
  }
}
```

---

## 🔗 Pipe Chaining and Parameters

### Q4: How do you chain pipes and pass parameters in Angular?

**Answer:**
Angular allows you to chain multiple pipes and pass parameters to customize their behavior.

**1. Pipe Chaining:**

```typescript
@Component({
  template: `
    <div>
      <!-- Multiple transformations in sequence -->
      <p>{{ birthday | date | uppercase }}</p>
      <!-- Output: JUNE 23, 2024 -->
      
      <p>{{ text | slice:0:10 | uppercase | reverse }}</p>
      <!-- Output: DLROW OLLE -->
      
      <p>{{ user | json | slice:0:50 | uppercase }}</p>
      <!-- Output: {"NAME":"JOHN","AGE":30,"CITY":"NEW YORK"} -->
    </div>
  `
})
export class PipeChainingComponent {
  birthday = new Date('2024-06-23');
  text = 'Hello World';
  user = { name: 'John', age: 30, city: 'New York' };
}
```

**2. Parameterizing Pipes:**

```typescript
@Component({
  template: `
    <div>
      <!-- Date pipe with format -->
      <p>{{ today | date:'fullDate' }}</p>
      <!-- Output: Sunday, June 23, 2024 -->
      
      <!-- Currency pipe with multiple parameters -->
      <p>{{ amount | currency:'EUR':'symbol':'1.2-2' }}</p>
      <!-- Output: €123.45 -->
      
      <!-- Number pipe with decimal places -->
      <p>{{ pi | number:'1.3-3' }}</p>
      <!-- Output: 3.142 -->
      
      <!-- Slice pipe with start and end -->
      <p>{{ items | slice:1:4 }}</p>
      <!-- Output: Banana, Cherry, Date -->
    </div>
  `
})
export class ParameterizedPipesComponent {
  today = new Date();
  amount = 123.456;
  pi = 3.14159;
  items = ['Apple', 'Banana', 'Cherry', 'Date', 'Elderberry'];
}
```

**3. Dynamic Pipe Parameters:**

```typescript
@Component({
  template: `
    <div>
      <select [(ngModel)]="selectedFormat">
        <option value="short">Short</option>
        <option value="medium">Medium</option>
        <option value="long">Long</option>
        <option value="full">Full</option>
      </select>
      
      <p>{{ today | date:selectedFormat }}</p>
      
      <input [(ngModel)]="decimalPlaces" type="number" min="0" max="5">
      <p>{{ pi | number:'1.' + decimalPlaces + '-' + decimalPlaces }}</p>
    </div>
  `
})
export class DynamicParametersComponent {
  today = new Date();
  pi = 3.14159;
  selectedFormat = 'medium';
  decimalPlaces = 2;
}
```

---

## ⚡ Performance Considerations

### Q5: What are the performance implications of using pipes in Angular?

**Answer:**
Understanding pipe performance is crucial for building efficient Angular applications.

**1. Pure vs Impure Pipes:**

```typescript
// Pure pipe (default) - runs only when input changes
@Pipe({
  name: 'pureExample',
  pure: true // This is the default
})
export class PureExamplePipe implements PipeTransform {
  transform(value: any): any {
    console.log('Pure pipe executed'); // Only logs when value changes
    return value.toUpperCase();
  }
}

// Impure pipe - runs on every change detection cycle
@Pipe({
  name: 'impureExample',
  pure: false
})
export class ImpureExamplePipe implements PipeTransform {
  transform(value: any): any {
    console.log('Impure pipe executed'); // Logs on every change detection
    return Math.random(); // Returns different value each time
  }
}
```

**2. Performance Best Practices:**

```typescript
@Component({
  template: `
    <!-- ❌ Bad: Heavy computation in template -->
    <div *ngFor="let item of items">
      {{ item | expensivePipe }}
    </div>
    
    <!-- ✅ Good: Pre-compute in component -->
    <div *ngFor="let item of processedItems">
      {{ item.processedValue }}
    </div>
    
    <!-- ✅ Good: Use trackBy for *ngFor -->
    <div *ngFor="let item of items; trackBy: trackByFn">
      {{ item | simplePipe }}
    </div>
  `
})
export class PerformanceExampleComponent {
  items = [/* large array */];
  processedItems: any[] = [];

  ngOnInit() {
    // Pre-process data in component
    this.processedItems = this.items.map(item => ({
      ...item,
      processedValue: this.expensiveTransformation(item)
    }));
  }

  trackByFn(index: number, item: any): any {
    return item.id; // Helps Angular track items efficiently
  }

  private expensiveTransformation(item: any): any {
    // Heavy computation moved to component
    return item.value * 2;
  }
}
```

**3. AsyncPipe Performance:**

```typescript
@Component({
  template: `
    <!-- ✅ Good: AsyncPipe handles subscription automatically -->
    <div *ngIf="user$ | async as user">
      <h2>{{ user.name }}</h2>
      <p>{{ user.email }}</p>
    </div>
    
    <!-- ❌ Bad: Manual subscription in component -->
    <div *ngIf="user">
      <h2>{{ user.name }}</h2>
      <p>{{ user.email }}</p>
    </div>
  `
})
export class AsyncPerformanceComponent {
  user$ = this.userService.getUser();
  user: any; // Don't do this

  ngOnInit() {
    // ❌ Bad: Manual subscription
    this.userService.getUser().subscribe(user => {
      this.user = user;
    });
  }
}
```

**4. Memoization for Expensive Pipes:**

```typescript
@Pipe({
  name: 'memoizedExpensive'
})
export class MemoizedExpensivePipe implements PipeTransform {
  private cache = new Map();

  transform(value: any): any {
    if (this.cache.has(value)) {
      return this.cache.get(value);
    }
    
    const result = this.expensiveOperation(value);
    this.cache.set(value, result);
    return result;
  }

  private expensiveOperation(value: any): any {
    // Simulate expensive operation
    return value * value * value;
  }
}
```

---

## 🎯 Best Practices

### Q6: What are the best practices for using Angular pipes?

**Answer:**

**1. Use Built-in Pipes When Possible:**

```typescript
// ✅ Good: Use built-in pipes
<p>{{ price | currency:'USD' }}</p>
<p>{{ date | date:'short' }}</p>
<p>{{ text | uppercase }}</p>

// ❌ Bad: Recreating built-in functionality
<p>{{ formatCurrency(price) }}</p>
<p>{{ formatDate(date) }}</p>
<p>{{ text.toUpperCase() }}</p>
```

**2. Keep Pipes Pure and Stateless:**

```typescript
// ✅ Good: Pure, stateless pipe
@Pipe({ name: 'multiply' })
export class MultiplyPipe implements PipeTransform {
  transform(value: number, multiplier: number): number {
    return value * multiplier;
  }
}

// ❌ Bad: Impure, stateful pipe
@Pipe({ name: 'badExample', pure: false })
export class BadExamplePipe implements PipeTransform {
  private counter = 0;
  
  transform(value: any): any {
    this.counter++; // Side effect
    return value + this.counter;
  }
}
```

**3. Handle Null and Undefined Values:**

```typescript
@Pipe({ name: 'safe' })
export class SafePipe implements PipeTransform {
  transform(value: any, defaultValue: any = ''): any {
    return value != null ? value : defaultValue;
  }
}

// Usage
<p>{{ user?.name | safe:'Unknown User' }}</p>
```

**4. Use TypeScript for Type Safety:**

```typescript
@Pipe({ name: 'filter' })
export class FilterPipe<T> implements PipeTransform {
  transform(items: T[], predicate: (item: T) => boolean): T[] {
    return items ? items.filter(predicate) : [];
  }
}

// Usage with type safety
<div *ngFor="let user of users | filter:isActiveUser">
  {{ user.name }}
</div>
```

**5. Test Your Pipes:**

```typescript
import { FilterPipe } from './filter.pipe';

describe('FilterPipe', () => {
  let pipe: FilterPipe<any>;

  beforeEach(() => {
    pipe = new FilterPipe();
  });

  it('should filter items based on predicate', () => {
    const items = [1, 2, 3, 4, 5];
    const result = pipe.transform(items, (item) => item > 3);
    expect(result).toEqual([4, 5]);
  });

  it('should return empty array for null input', () => {
    const result = pipe.transform(null, () => true);
    expect(result).toEqual([]);
  });
});
```

---

## ❓ Common Interview Questions

### Q7: What are common Angular pipes interview questions and answers?

**Answer:**

**Q: What's the difference between pure and impure pipes?**

**A:** Pure pipes run only when Angular detects a pure change to the input value (primitive values or object reference changes). Impure pipes run on every change detection cycle, regardless of input changes.

```typescript
// Pure pipe - efficient
@Pipe({ name: 'pure', pure: true })
export class PurePipe implements PipeTransform {
  transform(value: any): any {
    return value.toUpperCase();
  }
}

// Impure pipe - runs frequently
@Pipe({ name: 'impure', pure: false })
export class ImpurePipe implements PipeTransform {
  transform(value: any): any {
    return new Date().toISOString();
  }
}
```

**Q: How do you create a custom pipe that accepts multiple parameters?**

**A:** Define parameters in the transform method and pass them in the template:

```typescript
@Pipe({ name: 'format' })
export class FormatPipe implements PipeTransform {
  transform(value: any, format: string, prefix: string = ''): string {
    return `${prefix}${value}${format}`;
  }
}

// Usage: {{ name | format:'!' : 'Hello ' }}
```

**Q: Can you chain pipes in Angular?**

**A:** Yes, pipes can be chained using the pipe operator (|):

```html
{{ data | pipe1 | pipe2 | pipe3 }}
```

**Q: How does AsyncPipe work with observables?**

**A:** AsyncPipe automatically subscribes to observables and unsubscribes when the component is destroyed:

```typescript
// Component
user$ = this.userService.getUser();

// Template
<div *ngIf="user$ | async as user">
  {{ user.name }}
</div>
```

**Q: What happens if you don't handle null values in custom pipes?**

**A:** It can cause runtime errors. Always check for null/undefined values:

```typescript
transform(value: any): any {
  if (value == null) return '';
  return value.toString();
}
```

---

## 📚 Additional Resources

- [Angular Pipes - Official Docs](https://angular.io/guide/pipes)
- [Angular Built-in Pipes](https://angular.io/api/common#pipes)
- [Angular Pipe API](https://angular.io/api/core/Pipe)

---

[⬆️ Back to Top](#angular-pipes-interview-questions--answers-)
