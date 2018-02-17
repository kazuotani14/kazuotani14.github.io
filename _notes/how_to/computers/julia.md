# Julia

* Type-safe: when you can deduce the type of the output by the types of the inputs (not dependent on the values of the inputs). This allows the compiler to optimize code, which makes it faster

* Jupyter notebook - go to folder in terminal, `jupyter notebook`
* Use Latex notation and tab completion for unicode variables - `x\_0<tab>`
    * This works in Julia notebooks and REPL, but how can we get this to work in code?
* `?<command>` to get documentation
* `whos()` to see current workspace variables
* Simple performance checks
    * `tic(),toc()` to time functions
    * `@time` macro to time code and check memory usage
* [Reference for PyPlot](https://gist.github.com/gizmaa/7214002)

### Workflow

1. Test simple stuff in REPL, iPython notebook
2. Move stuff to module file
3. In REPL: `include Module.jl` to test `Module.*`
4. `workspace()` to clear everything, or just `include` again to overwrite with changes
