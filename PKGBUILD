# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Truocolo <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
# Maintainer: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Maintainer: Pellegrino Prevete (dvorak) <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Maintainer: l-koehler <lorenz.koehler@posteo.de>

_cc="clang"
_mmap_to_disk="false"
_compressor="cmix"
_pkg="fast-${_compressor}"
pkgname="${_pkg}"
pkgver=3
_commit="e196e65fa09b2e655e8fb74e6c9944c2fc27eced"
pkgrel=1
_pkgdesc=(
  "Faster implementation of cmix-hp."
)
pkgdesc="${_pkgdesc[*]}"
arch=(
  'x86_64'
  'arm'
  'aarch64'
  'armv7l'
  'armv6l'
  'mips'
  'powerpc'
  'pentium4'
  'i686'
)
_http="https://github.com"
_ns="saurabhk"
url="${_http}/${_ns}/${_pkg}"
license=(
  'GPL-3.0-only'
)
options=(
  !debug
)
depends=()
makedepends=(
  "${_cc}"
  'make'
  # 'upx'
)
if [[ "${_cc}" == "clang" ]]; then
  makedepends+=(
    "llvm"
  )
fi
provides=(
  "${_compressor}"
  "${_compressor}-hp"
)
conflicts=(
  "${_compressor}"
  "${_compressor}-hp"
)
optdepends=(
  'tar: compress multiple files by making a tarball'
)
_http="https://github.com"
_url="${_http}/${_ns}/${_pkg}"
_tag="${_commit}"
_tar="${_pkg}-${_tag}"
source=(
  "${_tar}.zip::${url}/archive/${_commit}.zip"
)
sha256sums=(
  'dac94df6bb325ca33460dcd97c27f94ee5a9d50e58ded102a0f916cde2d8053a'
)

_cxx_get() {
  local \
    _cxx \
    _ccxs=()
  _cxxs=(
    "cxx"
    "g++"
    "clang++"
  ) 
  _cxx="$( \
    command \
      -v \
      "${_cxxs[@]}" | \
      awk \
        '{print $1}')"
  echo \
    "${_cxx}"
}

prepare() {
  local \
    _cxx
  cd \
    "${_tar}"
  _cxx="$( \
    _cxx_get)"
  msg \
    "CXX compiler: '${_cxx}'"
  sed \
    "s%clang++-13%$(basename "${_cxx}")%g;"  \
    -i \
    "${srcdir}/${_tar}/makefile"
  if [[ "${_mmap_to_disk}" == "false" ]]; then
    sed \
      "s%bool mmap_to_disk = true%bool mmap_to_disk = false%g;" \
      -i \
      "src/models/ppmd.cpp"
  fi
}

build() {
  local \
    _cxx \
    _cflags_define=() \
    _seed \
    _update_limit=3000
  _seed=923
  _update_Limit=3000
  _ccx="$( \
    _cxx_get)"
  _cflags_define+=(
    -DSEED=${_seed}
    -DUPDATE_LIMIT=${_update_limit}
  )
  cd \
    "${_tar}"
  export \
    CC="${_cxx}" \
    CXX="${_cxx}" \
    CFLAGS_DEFINES="${_cflags_define[*]}"
  CFLAGS_DEFINES="${_cflags_define[*]}" \
  CC="${_cxx}" \
  CXX="${_cxx}" \
  make \
    prof_gen \
    -j
  ./cmix \
    -c \
      "./prof_input/input" \
      "./prof_comp" > \
      "./prof_output"
  rm \
    "./prof_comp" \
    "./prof_output"
  ls
  if [[ "${_cc}" == "clang" ]]; then
    llvm-profdata \
      merge \
        -output="default.profdata" \
	"./pgo_data/"*
    mv \
      default.profdata \
      pgo_data
  fi
  CFLAGS_DEFINES="${_cflags_define[*]}" \
  CC="${_cxx}" \
  CXX="${_cxx}" \
  make \
    prof_use \
    -j
}

package() {
  cd \
    "${_tar}"
  mkdir \
    -p \
    "${pkgdir}/usr/bin"
  mv \
    "./${_compressor}" \
    "${pkgdir}/usr/bin"
}
