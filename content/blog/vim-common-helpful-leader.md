---
title: Vim Commmon Helpful Leader
date: 2014-11-12
tags: vim, vim-tips, vim-leaders 
---

I have really enjoyed using Vim for a month now, and I have to admit that I
love the feeling of learning new things from Vim everyday. Today I would love to
share some of my favorite Vim Leaders that I found they're extremely helpful. You
can add these into your .vimrc file, and give it a try.

- Open another file in same directory

        map <Leader>e :e <C-R>=escape(expand("%:p:h"),' ') . '/'<CR>
        map <Leader>s :split <C-R>=escape(expand("%:p:h"), ' ') . '/'<CR>
        map <Leader>v :vnew <C-R>=escape(expand("%:p:h"), ' ') . '/'<CR>

- Rename current file. This is a little bit more complex that requires us do
   define a helper function.

        function! RenameFile()
            let old_name = expand('%')
            let new_name = input('New file name: ', expand('%'), 'file')
            if new_name != '' && new_name != old_name
                exec ':saveas ' . new_name
                exec ':silent !rm ' . old_name
                redraw!
            endif
        endfunction
        map <Leader>n :call RenameFile()<cr>

