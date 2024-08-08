# Install nvim--------------------------------------------------------------------------------------------------------------

git clone https://github.com/neovim/neovim.git
cd neovim
git checkout stable
make CMAKE_BUILD_TYPE=Release
sudo make install
nvim --version

# Clean nvim folder--------------------------------------------------------------------------------------------------------------

cd .. 
sudo rm -rf neovim/

# Install font------------------------------------------------------------------------------------------------------------

sudo apt install fonts-jetbrains-mono

#!/bin/bash

# Remove Neovim previous configurations 
echo "Removing existing Neovim configuration and packer.nvim..."
rm -rf ~/.config/nvim
rm -rf ~/.local/share/nvim


# Clone NvChad Configuration -- ------------------------------------------------------------------------------------------------------------
echo "Cloning NvChad configuration..."
git clone -b v2.0 https://github.com/NvChad/NvChad ~/.config/nvim --depth 1



echo "Setup complete. Please open Neovim and run :PackerSync to ensure all plugins are installed and updated properly."

# At this point you got many errors, just open nvim, use :PackerSync and close it one or two times :)

