# nvim-various-textobjs
Bundle of about a dozen custom text objects for Neovim. Includes text objects for: indentation, number, value, diagnostic, markdown link, double square bracket, and many more.

<!--toc:start-->
- [List of Text Objects](#list-of-text-objects)
- [Installation](#installation)
- [Configuration](#configuration)
- [Advanced Usage](#advanced-usage)
- [Roadmap](#roadmap)
- [Credits](#credits)
- [About me](#about-me)
<!--toc:end-->

## List of Text Objects

| textobj                | description                                                                                   | inner / outer                                                                             | forward-seeking | default keymaps          | filetypes                         |
|:---------------------- |:--------------------------------------------------------------------------------------------- |:----------------------------------------------------------------------------------------- |:--------------- |:------------------------:|:--------------------------------- |
| indentation            | lines with same amount of indentation&nbsp;                                                   | [see overview from vim-indent-object](https://github.com/michaeljsmith/vim-indent-object) | no              | `ii`, `ia`, `aI`, (`iI`) | all                               |
| value                  | value of key-value pair, or right side of a variable assignment (excluding trailing comments) | outer includes trailing commas or semicolons                                              | yes             | `iv`, `av`               | all                               |
| number                 | numbers, similar to `&lt;C-a&gt;`                                                             | inner: only pure digits, outer: number including minus sign and decimal point             | yes             | `in`, `an`               | all                               |
| diagnostic             | LSP diagnostic (requires built-in LSP)                                                        | -                                                                                         | yes             | `!`                      | all                               |
| near end of line       | from cursor position to end of line, minus one character                                      | -                                                                                         | no              | `n`                      | all                               |
| Markdown link          | link like `[title](url)`                                                                      | inner: only the link title (between the `[]`)&nbsp;                                       | yes             | `il`, `al`               | markdown                          |
| Markdown code block    | fenced code (enclosed by three backticks)                                                     | outer includes the enclosing backticks                                                    | yes             | `iC`, `aC`               | markdown                          |
| CSS selector           | class in CSS, like `.my-class`                                                                | outer includes trailing comma and space                                                   | yes             | `ic`, `ac`               | css, scss                         |
| JavaScript regex       | regex pattern                                                                                 | <div>outer includes the slashes and any flags</div>                                       | yes             | `i/`, `a/`               | javascript, typescript            |
| double square brackets | text enclosed by                                                                              | outer includes the four square brackets                                                   | yes             | `iD`, `aD`               | lua, shell, neorg, markdown       |
| rest of paragraph      | like `}`, but linewise                                                                        | -                                                                                         | no              | `r`                      | all                               |
| subword                | like `iw`, but never treating `-`, `_` or `.` as word delimiters                              | analog to `iw` / `aw`                                                                     | no              | `iS`, `aS`               | all                               |

## Installation

```lua
-- packer
use {
	"chrisgrieser/nvim-various-textobjs",
	config = function () 
		require("various-textobj").setup({ useDefaultKeymaps = true })
	end,
}

-- lazy.nvim
{
	"chrisgrieser/nvim-various-textobjs",
	config = function () 
		require("various-textobj").setup({ useDefaultKeymaps = true })
	end,
},
```

## Configuration
The `.setup()` call is optional and only required when you want to change the default configuration:

```lua
require("various-textobjs").setup {
	lookForwardLines = 5, -- default: 5. Set to 0 to only look in the current line
	useDefaultKeymaps = false, -- Use suggested keymaps (see README). Default: false.
}
```

If you want to set keybindings yourself, you can define them like this. Note that for the text objects that differentiate between outer and inner, the parameters are required and all boolean (`true` always meaning "inner"). 

```lua
-- example: `an` for outer number, `in` for inner number
vim.keymap.set({"o", "x"}, "an", function () require("various-textobjs").number(false) end)
vim.keymap.set({"o", "x"}, "in", function () require("various-textobjs").number(true) end)
```

## Advanced Usage
You can also use the text objects as input for small snippets by yanking them and using `getreg()`. The following example uses the outer regex text object to retrieve pattern, flags, and replacement value of the next regex, and opens [regex101](https://regex101.com/) prefilled with them:

```lua
keymap("n", "gR", function()
	require("various-textobjs").jsRegex(false) -- set visual selection to outer regex
	vim.cmd.normal { '"zy', bang = true }
	local regex = vim.fn.getreg("z")
	local pattern = regex:match("/(.*)/")
	local flags = regex:match("/.*/(.*)")
	local replacement = fn.getline("."):match('replace ?%(/.*/.*, ?"(.-)"')
	-- https://github.com/firasdib/Regex101/wiki/FAQ#how-to-prefill-the-fields-on-the-interface-via-url
	local url = "https://regex101.com/?regex=" .. pattern .. "&flags=" .. flags
	if replacement then url = url .. "&subst=" .. replacement end

	local opener
	if vim.fn.has("macunix") then
		opener = "open"
	elseif vim.fn.has("unix") then
		opener = "xdg-open"
	elseif vim.fn.has("win64") or fn.has("win32") then
		opener = "start"
	end
	os.execute(opener .. "'" .. url .. "'")
end, { desc = "Open next js regex in regex101" })
```

## Roadmap
- [ ] Figure out how to make dot-repeatability work. (Pointers are welcome.)

## Credits
Thanks to the Valuable Dev for [their blogpost on how to get started with creating custom text objects](https://thevaluable.dev/vim-create-text-objects/).

<!-- vale Google.FirstPerson = NO -->
## About me
In my day job, I am a sociologist studying the social mechanisms underlying the digital economy. For my PhD project, I investigate the governance of the app economy and how software ecosystems manage the tension between innovation and compatibility. If you are interested in this subject, feel free to get in touch.

__Profiles__
- [Discord](https://discordapp.com/users/462774483044794368/)
- [Academic Website](https://chris-grieser.de/)
- [GitHub](https://github.com/chrisgrieser/)
- [Twitter](https://twitter.com/pseudo_meta)
- [ResearchGate](https://www.researchgate.net/profile/Christopher-Grieser)
- [LinkedIn](https://www.linkedin.com/in/christopher-grieser-ba693b17a/)
