--Install nvim--------------------------------------------------------------------------------------------------------------

git clone https://github.com/neovim/neovim.git
cd neovim
git checkout stable
make CMAKE_BUILD_TYPE=Release
sudo make install
nvim --version

--Clean nvim folder--------------------------------------------------------------------------------------------------------------

cd .. 
sudo rm -rf neovim/

-Install font------------------------------------------------------------------------------------------------------------

sudo apt install fonts-jetbrains-mono

-- Clone NvChad Configuration -- ------------------------------------------------------------------------------------------------------------
rm -rf ~/.config/nvim
git clone -b v2.0 https://github.com/NvChad/NvChad ~/.config/nvim --depth 1

-- Create and Edit Plugin Configuration Files -----------------------------------------------------------------------------------------------

mkdir -p ~/.config/nvim/lua/custom
cat <<EOL > ~/.config/nvim/lua/custom/plugins.lua
-- ~/.config/nvim/lua/custom/plugins.lua
return require("packer").startup(function()
  -- NvChad plugins
  use "wbthomason/packer.nvim"

  -- LSP
  use "neovim/nvim-lspconfig" -- Collection of configurations for built-in LSP

  -- Treesitter for syntax highlighting
  use {
    "nvim-treesitter/nvim-treesitter",
    run = ":TSUpdate",
  }

  -- Completion
  use "hrsh7th/nvim-cmp" -- Autocompletion plugin
  use "hrsh7th/cmp-nvim-lsp" -- LSP source for nvim-cmp
  use "hrsh7th/cmp-buffer" -- Buffer source for nvim-cmp
  use "hrsh7th/cmp-path" -- Path source for nvim-cmp
  use "hrsh7th/cmp-cmdline" -- Cmdline source for nvim-cmp

  -- Debugging
  use "mfussenegger/nvim-dap" -- Debug Adapter Protocol client
  use "rcarriga/nvim-dap-ui" -- UI for nvim-dap

  -- Optional: Debugging for Python and C/C++
  use "leoluz/nvim-dap-go" -- For Go debugging (if needed)
end)
EOL

cat <<EOL > ~/.config/nvim/lua/custom/lsp.lua
-- ~/.config/nvim/lua/custom/lsp.lua
local lspconfig = require("lspconfig")

-- Configure LSP servers
lspconfig.pyright.setup{}  -- Python
lspconfig.clangd.setup{}  -- C/C++

-- Setup completion
local cmp = require("cmp")

cmp.setup({
  sources = {
    { name = "nvim_lsp" },
    { name = "buffer" },
    { name = "path" },
    { name = "cmdline" }
  }
})
EOL

cat <<EOL > ~/.config/nvim/lua/custom/treesitter.lua
-- ~/.config/nvim/lua/custom/treesitter.lua
require("nvim-treesitter.configs").setup({
  ensure_installed = {"python", "cpp", "c"}, -- Ensure parsers are installed
  highlight = {
    enable = true, -- Enable syntax highlighting
  },
  indent = {
    enable = true, -- Enable indentation
  },
})
EOL

cat <<EOL > ~/.config/nvim/lua/custom/dap.lua
-- ~/.config/nvim/lua/custom/dap.lua
local dap = require("dap")

-- Python debug configuration
dap.adapters.python = {
  type = 'executable',
  command = 'python',
  args = { '-m', 'debugpy.adapter' },
}

dap.configurations.python = {
  {
    type = 'python',
    request = 'launch',
    name = 'Launch file',
    program = '${file}',
    pythonPath = function()
      return '/usr/bin/python'
    end,
  },
}

-- C/C++ debug configuration
dap.adapters.cppdbg = {
  id = 'cppdbg',
  type = 'executable',
  command = '/path/to/your/cpptools/extension/debugAdapters/bin/OpenDebugAD7',
}

dap.configurations.cpp = {
  {
    name = 'Debug',
    type = 'cppdbg',
    request = 'launch',
    program = '${fileDirname}/${fileBasenameNoExtension}',
    cwd = '${workspaceFolder}',
    stopAtEntry = true,
    setupCommands = {
      {
        description = 'Enable pretty-printing for gdb',
        text = '-enable-pretty-printing',
        ignoreFailures = true,
      },
    },
    miDebuggerPath = '/usr/bin/gdb',
  },
}

dap.configurations.c = dap.configurations.cpp
EOL

------------------------------------------------------------------------------------------------------------

nvim --headless -c 'autocmd User PackerComplete quitall' -c 'PackerInstall'
nvim --headless -c 'autocmd User PackerComplete quitall' -c 'PackerUpdate'
