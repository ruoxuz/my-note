#### html
    <mat-select [(value)]="profile.birthdayYear" name="birthdayYear"
                                        [(ngModel)]="profile.birthdayYear" (opened)="selectOpened()">
                              <mat-option *ngFor="let item of yearMap | keyvalue"
                                          [value]="item.key" class="birthdayYearOptGroup">
                                {{item.value}}
                              </mat-option>
                            </mat-select>

#### ts
    selectOpened(): void {
    if (this.profile.birthdayYear === null || this.profile.birthdayYear === undefined) {
      document.querySelectorAll('.birthdayYearOptGroup').forEach(item => {
        if (item.getAttribute('ng-reflect-value') === '1995' || item.getAttribute('id') === 'mat-option-93') {
          item.scrollIntoView(true);
        }
      });
    }
  }