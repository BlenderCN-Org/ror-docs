Composing nice and clean commits is just as important as writing nice and clean code; it helps your co-developers to easily see what you changed and what are you tried to achieve with the change.

### Rule #1: Commit only affected files together
When you commit 2 or more files at once, you're basically saying: "my change affected all these files; if one was missing, the commit would break the build". Which also means: if you changed the same thing in multiple files and those changes do not affect each other, you should commit the files one by one. Examples:
* If you move a forward declaration from some header to ForwardDeclarations.h, you should commit both files at once. Everyone will plainly see what happened and committing only one file would break the build.
* If you do a header guard cleanup in several files, you should commit them one by one (but nobody would probably complain if you committed them at once).
* if you do both aforementioned changes at the same time (it's often tempting to do cleanups on-the-way), take care not to commit it all at once. The 'forward declaration' change should be separated from the rest.

### Rule #2: Change one thing at a time 
Example: you've changed a complicated algorithm in a middle of a function, but you've also cleaned the formatting in the rest of the function. Unfortunately, versioning system don't understand the code, so anyone viewing your commit will see the whole function body marked in red and green and will have a hard time understanding what 'changed' as in 'code change' and what changed as in 'cleanup'. And, it will be your fault.

Sticking to this rule requires you to think ahead a bit: If you've already made the mistake from the example above, then you have no choice but to manually swap away your changes, revert the file and paste the changed code back.

### Rule #3: Write meaningful commit messages 
Commit message is the first thing your co-devs will look at, and it should give them a complete idea what the commit does (but just an idea, you don't need to re-tell what the code already says).

Additionally, here's a list of prefixes which should be on every commit message. HINT: If your change doesn't match either of these, then you probably changed too much at once :)

* :construction: `:construction:` Work in progress (WIP).
* :bug: `:bug:` Bugfix.
* 🎮 `:video_game:` An adjustment/change of gameplay.
* 🔧 `:wrench:` Internal change (codechange) which doesn't affect user experience.
* ✨ `:sparkles:` New feature.
* 📚 `:books:` Change of documentation only.
* 📐 `:triangular_ruler:` Code cleanup (codechange only)
* 🎌 `:crossed_flags:` Language only commit
* ✅ `:white_check_mark:` Merge of pull-request (PR)
* 🔙 `:back:` Revert commit
* 🎉 ```:tada:``` Release!

Happy coding!
