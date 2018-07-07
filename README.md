# Angular 6 Custom validator ( Confirm Password ) 
How to implement custom confirm password validator in Angular 6 (Final) (template driven form)

 * This directive will be allow to validate two controls value  with same unique Attribute
 * [equalTo]
 * this directive return AbstractControl with new custom validation {equalTo : _value}
 * and return NULL if match value
 * HTML TEMPLATE:
 ```<form #form="ngForm">
    <label for ="password">Enter your password</label>
    <input
        required
        type="text"
        name="password"
        [(ngModel)] ="user.password"
         equalTo="{{user.confirmPassword}}" //custom validation
        id="password"
        #password="ngModel">

    <label for="confirmPassword">Enter confirm </label>
    <input
        required
        type="text"
        equalTo="{{user.password}}" //custom validation
        name="confirmPassword"
        [(ngModel)]="user.confirmPassword"
        id="confirmPassword"
        #confirmPassword="ngModel">

     {{confirmPassword.errors | json}} 
     OUTPUT: { "equalTo": "pas" }
</form>


import { Directive, Input, OnChanges, SimpleChanges } from '@angular/core';
import { AbstractControl, NG_VALIDATORS, Validator, ValidatorFn, Validators } from '@angular/forms';

export function equalToValidator(val: string): ValidatorFn {
  return (control: AbstractControl): { [key: string]: any } => {
    /* this is where you want to put all your custom validation logic
       REMINDER: returning null means valid, so check for invalid */
    if (control.value !== val && val !== '') {
      return { 'equalTo': control.value };
    }
    return null;
  };
}

@Directive({
  selector: '[equalTo]',
  providers: [{ provide: NG_VALIDATORS, useExisting: EqualToValidatorDirective, multi: true }]
})
export class EqualToValidatorDirective implements Validator, OnChanges {
  // Input store last value for equalTo
  @Input() equalTo: string;
  private valFn = Validators.nullValidator;
  // define null Validators REF:(ttps://angular.io/api/forms/Validators#nullValidator
  ngOnChanges(changes: SimpleChanges): void {
    const change = changes['equalTo'];
    if (change) {
      // if changes allowed check validation
      this.valFn = equalToValidator(change.currentValue);
    } else {
      // return null
      this.valFn = Validators.nullValidator;
    }
  }
  // Validator interface can hold AbstractClass and AbstractControl are form control
  // REF: https://angular.io/api/forms/AbstractControl
  validate(control: AbstractControl): { [key: string]: any } {
    return this.valFn(control);
  }
}
```
