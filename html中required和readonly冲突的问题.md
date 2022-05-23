把readonly写在class里，script里添加

    $(".readonly").on('keydown paste focus mousedown', function(e){
    if(e.keyCode != 9) // ignore tab
        e.preventDefault();
    });