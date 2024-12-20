pkgname=("android-system-tools-mkbootimg-git")
pkgrel=1
pkgver=0.0.1
arch=('any')

set_variables() {
  mkbootimg_project_path="platform/system/tools/mkbootimg"
  mkbootimg_folder="${srcdir}/$(realpath -m --relative-to "platform" "$mkbootimg_project_path")"
}

prepare() {
  set_variables

  cd "$srcdir" || exit 1

  repo init -u https://android.googlesource.com/platform/manifest

  if [ -n "$SKIP_SYNC" ]; then
    repo sync "$mkbootimg_project_path" -j"$(nproc)"
  fi
}

package_android-system-tools-mkbootimg-git() {
  depends=("python" "python-pyelftools")

  set_variables

  install_files=(
    "mkbootimg.py"
    "repack_bootimg.py"
    "unpack_bootimg.py"
  )

  install_directories=(
    "gki"
  )

  local install_dest="$pkgdir/opt/android/system/tools/mkbootimg"
  local relative_install_dest

  for folder in "${install_directories[@]}"; do
    local absolute_path="$mkbootimg_folder/$folder"

    mapfile -t files < <(find "$absolute_path" -type f)

    for file in "${files[@]}"; do
      relative_install_dest="$(realpath --relative-to "$mkbootimg_folder" "$file")"

      install -v -D "$file" "$install_dest/$relative_install_dest"
    done
  done

  for f in "${install_files[@]}"; do
    install -v -D "$mkbootimg_folder/$f" "$install_dest/$f"
  done

  bin_directory="$pkgdir/usr/local/bin"

  for f in "${install_files[@]}"; do
    local symbolic_link_path="$bin_directory/$(basename "$f" ".py")"

    # Install parent directory
    install -d "$(dirname "$symbolic_link_path")"

    # Create the symbolic link
    ln -s -v -f "/$(realpath -m --relative-base "$pkgdir" "$install_dest")/$f" "$symbolic_link_path"

    # Make the script executable
#     chmod +x "$symbolic_link_path"
  done
}

