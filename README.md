# Exercise 2 - Scoping

1. This exercise is a work-log (aka timesheet) application. Run the fixed version of the exercise ("ex2-fixed.html") in your browser and play around to see the expected behavior.

2. For this exercise, you should only need to make changes to "ex2.js", not the HTML or CSS. You should not need to create new functions or reorganize significant chunks of code for this exercise; only a few spot changes and a few lines of code are necessary.

3. There are several things that can be improved in "ex2.js", including:

	- are there anonymous function expressions which could be improved with a lexical name?
	- are there usages of `var` which are more appropriate to be declared with `let` or `const`? Do any literal values need to be made into `const`ant declarations?
	- should certain variable declarations be contained in explicit blocks of scope?

4. **BONUS:** How would you describe to a coworker or boss the improvements in readability after applying your knowledge of scoped declarations to this code? Write out a few sentences.


# Exercise 3 - Hoisting

1. This exercise is a work-log (aka timesheet) application. Run the fixed version of the exercise ("ex3-fixed.html") in your browser and play around to see the expected behavior.

2. For this exercise, you should only need to make changes to "ex3.js", not the HTML or CSS. You should only need to do minor reorganization of code for this exercise.

3. There are several things that can be improved in "ex3.js", including:

	- are there function expressions that should be function declarations instead?
	- can function hoisting be leveraged to improve readability by relocating executable code?

4. **BONUS:** How would you describe to a coworker or boss the improvements in readability after applying your knowledge of hoisting to this code? Write out a few sentences.


# Exercise 4

1. This exercise is a work-log (aka timesheet) application. Run the fixed version of the exercise ("ex4-fixed.html") in your browser and play around to see the expected behavior.

2. For this exercise, you should only need to make changes to "ex4.js", not the HTML or CSS. You should only need to add a few lines of code.

3. A feature has been been partially added to this application, but you need to complete it:

	- To keep the UI clean, work descriptions are already visually shortened if the exceed a certain length. However, a shortened description should be able to expand to its full length if desired. The cursor should provide a visible hint that a shortened description can be clicked to expand it.
	- There are of course multiple ways you could implement such a feature. The intent here is specifically for your solution to **demonstrate a usage of closure**.

4. A new function `setupWorkDescription(..)` has been added, with a `// TODO` comment marking where your code should go. Using the same click handler pattern found in `initUI()`, add a click handler that:

	- removes the visual hint of click-to-expand (hint: `removeClass(..)`)
	- removes the click handler (hint: `off(..)`)
	- expands the description (hint: `text(..)`)

5. **BONUS:** There are at least two other ways to implement this feature without closure. Experiment with alternate implementations. What are the pros/cons of these other approaches?

# Exercise 5

1. This exercise is a work-log (aka timesheet) application. Run the fixed version of the exercise ("ex5-fixed.html") in your browser and play around to see the expected behavior.

2. For this exercise, you should only need to make changes to "ex5.js", not the HTML or CSS. The changes here will involve a fair bit of reorganization and restructuring of code, but you don't need to significantly new logic (or features).

	**Note:** This exercise is broken into 3 parts, and the solution files that correspond are "ex5-fixed.js", "ex5-fixed-b.js", and "ex5-fixed-c.js", respectively. Make sure you allow yourself enough time to get through all 3 parts. If you find yourself stuck too long on one part, grab the solution code from the appropriate file and use it as the starting point to move onto the next part of the exercise.

	Also, you'll most likely want to track these changes in separate files ("ex5-1.js", "ex5-2.js", etc), so that you'll be able to independently compare each of your solutions to the provided solution files.

3. **PART 1:** (~5 minutes)

	The code for our application so far is all hanging out in the global namespace as a bunch of variables and functions. This is messy. We should use what we learned about the module pattern to clean this up!

	- create a singleton module instance for your application, named `App`, wrapped around the all the variables and functions in "ex5.js", with a public API including the following methods:
		- `init()` (reference to `initUI()`)
		- `addProject(..)`
		- `addWorkToProject(..)` (only exposed for now so it's easier to test our code)
	- hints:
		- the call to `App.init()` and the hard-coded data (calls to `App.addProject(..)`) should remain external to the module definition

4. **PART 2:** (~15 minutes)

	The `App` module from Part 1 is a decent start to organizing our application. But all the data operations and the UI (DOM, etc) operations are all mixed together. Also, there are a few helpers which are stateless and could pulled out into a separate object/namespace. We should clean this up some more!

	- create a simple namespace object called `Helpers`, and move the following to it:
		- `validateWorkEntry(..)`
		- `formatWorkDescription(..)`
		- `formatTime(..)`
		- the 3 related "constants" (as properties)
	- create another module instance to move the UI/DOM related code to, named `UI`, by calling a module factory function (e.g. `setupUI()`), with (only) the following API methods:
		- `init()` (reference to `initUI()`)
		- `addProjectToList(..)`
		- `addProjectSelection(..)`
		- `addWorkEntryToList(..)`
		- `updateProjectTotalTime(..)`
		- `updateWorkLogTotalTime(..)`
	- `App` should now have (only) the following public API methods:
		- `addProject(..)`
		- `addWorkToProject(..)`
		- any methods (e.g. `getWorkEntryCount(..)` and `getWorkEntryLocation(..)`) necessary to expose minimal information about the data model to `UI` so insertion of DOM elements in the proper location is possible
	- change `App` to be created NOT as a singleton as before, but with a module factory function (e.g. `setupApp(..)`), which takes the `UI` instance as an argument; we still only have one instance of this module, but we could in theory (e.g. for testing purposes) want multiple instances at some point
	- separate any logic that deals with UI/DOM elements into `UI` methods, but leave all data model related logic in `App`'s methods
	- hints:
		- `UI` should track all DOM elements it creates for projects and work entries, stored by their respective Ids, instead of storing those element references in the data model
		- only pass the minimal data necessary (i.e. an entity Id) between `UI` and `App` methods; maintain the abstracted separation as much as possible
		- method(s) in `App` will need to call method(s) in `UI`, and vice versa; this circular relationship is intentional in our architecture
		- think carefully about the right place to store each piece of state information (i.e. total time for a project, total overall time, etc)

5. **PART 3:** (~10 minutes)

	By now, you should be feeling more confident about using the module pattern to organize your code cleanly. But there's one more level of abstraction we can create that will help even more: we can create a module instance (with a simple API!) for each project entry in the data model. That way, the details of how a project tracks its data do not pollute the rest of the application logic.

	- create a module factory function for projects, named `Project(..)`, which takes a single argument `description`
	- each project instance should now include (only) the following public API methods:
		- `getId()`
		- `getDescription()`
		- `getTime()`
		- `addWork(..)`
		- `getWorkEntryCount()`
		- `getWorkEntryLocation(..)`
	- move logic from `App` into `Project` that relates to managing the internal state of each project; `App` should now only store and use module instances of `Project(..)`
	- similarly, `UI`'s methods should now only be passed `Project(..)` instances
	- hints:
		- think about the simplest way to store and manage project state information
		- think about the parts of `App.addWorkToProject(..)` that should now be moved to `addWork(..)` on the project instance, and how to keep these two pieces separate

6. **BONUS:** Consider how the code in `UI` is hard-coded to very specific DOM elements and structure; this creates some brittleness to future HTML/CSS changes. How can you refactor the `setupUI` function / `UI` instance to be more configurable and less brittle?


# Exercise 6

1. This exercise is a work-log (aka timesheet) application. Run the fixed version of the exercise ("ex6-fixed.html") in your browser and play around to see the expected behavior.

2. For this exercise, you should only need to make changes to "ex6.js", not the HTML or CSS. The changes are only a few spot changes to code, nothing major.

3. The methods in `Helper` use hard-coded lexical references to their own context object. Let's clean *this* up by using what we learned about `this`!

4. **BONUS:** Can you think of any situations where the `this` references in `Helper` methods might be less reliable (require hard binding, for example?) Are there any other parts of the application that you would refactor to use `this` oriented code?


# Exercise 7

1. This exercise is a work-log (aka timesheet) application. Run the fixed version of the exercise ("ex7-fixed.html") in your browser and play around to see the expected behavior.

2. For this exercise, you should only need to make changes to "ex7.js", not the HTML or CSS. The changes are only a few spot changes to code, nothing major.

3. Change `Project(..)` from being a module factory function to being a `prototype`-style constructor function. All project data should be stored on the `this` instances and all methods should "inherited" from the `Project.prototype`.

4. **BONUS:** How might you move `sortTimeDescending(..)` to `Helpers`, and then have `Project` inherit from `Helpers`? Would there be any benefits to restructuring `App` or `UI` to be `prototype`-oriented as well?
