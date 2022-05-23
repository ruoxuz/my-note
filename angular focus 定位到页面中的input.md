1. html

        <input #emailElf #email="ngModel" type="email" class="form-control input-height" name="mail" [(ngModel)]="profile.mail" required> 
        
2. ts

        @ViewChild('emailElf') emailElf!: ElementRef;
        
        const ele = this.emailElf.nativeElement;
        if (ele) {
            ele.focus();
        }