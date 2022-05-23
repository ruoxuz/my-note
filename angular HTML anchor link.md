        const routerOptions: ExtraOptions = {
          useHash: false,
          anchorScrolling: 'enabled',
          // ...any other options you'd like to use
        };
        
        // then just import your RouterModule with these options
        
        RouterModule.forRoot(MY_APP_ROUTES, routerOptions)

[link](https://stackoverflow.com/questions/50836497/using-html-anchor-link-id-in-angular-6)