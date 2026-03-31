---
mainImage: ../../../images/part-7.svg
part: 7
letter: a
lang: en
---

<div class="content">

The exercises in this seventh part of the course differ a bit from the ones before. In this and the next chapter, as usual, there are [exercises related to the theory of the chapter](/en/part7/react_router#exercises-7-1-7-3).

In addition to the exercises in this and the next chapter, there are a series of exercises in which we'll be revising what we've learned during the whole course, by expanding the BlogList application, which we worked on during parts 4 and 5.

### React Router

This section has been moved to the [part 5](/en/part5/react_router_ui_frameworks). You still may do the below exercises.

</div>

<div class="tasks">

### Exercises 7.1.-7.3.

Let's return to working with anecdotes. Use the redux-free anecdote app found in the repository <https://github.com/fullstack-hy2020/routed-anecdotes> as the starting point for the exercises.

If you clone the project into an existing git repository, remember to <i>delete the git configuration of the cloned application:</i>

```bash
cd routed-anecdotes   // go first to directory of the cloned repository
rm -rf .git
```

The application starts the usual way, but first, you need to install its dependencies:

```bash
npm install
npm run dev
```

#### 7.1: Routed Anecdotes, step 1

Add React Router to the application so that by clicking links in the <i>Menu</i> component the view can be changed.

At the root of the application, meaning the path _/_, show the list of anecdotes:

![browser at baseURL showing anecdotes and footer](../../assets/teht/40.png)

The <i>Footer</i> component should always be visible at the bottom.

The creation of a new anecdote should happen e.g. in the path <i>create</i>:

![browser anecdotes /create shows create form](../../assets/teht/41.png)

#### 7.2: Routed Anecdotes, step 2

Implement a view for showing a single anecdote:

![browser /anecdotes/number showing single anecdote](../../assets/teht/42.png)

Navigating to the page showing the single anecdote is done by clicking the name of that anecdote:

![browser showing previous link that was clicked](../../assets/teht/43.png)

#### 7.3: Routed Anecdotes, step3

The default functionality of the creation form is quite confusing because nothing seems to be happening after creating a new anecdote using the form.

Improve the functionality such that after creating a new anecdote the application transitions automatically to showing the view for all anecdotes <i>and</i> the user is shown a notification informing them of this successful creation for the next five seconds:

![browser anecdotes showing success message for adding anecdote](../../assets/teht/44.png)

</div>
