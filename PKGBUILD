# Maintainer: Matthew McGinn <mamcgi@gmail.com>
# Contributor: Håvard Pettersson <mail@haavard.me>
# Contributor: Andrew Stubbs <andrew.stubbs@gmail.com>

_nodeversion=20
pkgname=balena-etcher
_pkgname=etcher
pkgver=1.19.17
pkgrel=1
epoch=2
pkgdesc='Flash OS images to SD cards & USB drives, safely and easily'
arch=('x86_64' 'i686' 'armv7h' 'aarch64')
_github_url='https://github.com/balena-io/etcher'
url='https://balena.io/etcher'
license=(Apache)
depends=()
makedepends=("git" "python" "nvm")
optdepends=("libnotify: for notifications")
conflicts=("${_pkgname}"
  "${_pkgname}-git"
  "${_pkgname}-bin"
)
options=('!strip')
source=("etcher::git+https://github.com/balena-io/${_pkgname}.git#tag=v${pkgver}"
        "${pkgname}.desktop"
        )
sha256sums=('a5c083015beffb1f749d2300db56a9d6ee0f20bef27822c7b0a5f6004e39dd65'
            '6c5fb48aeb636272689c86d7cf9beea4515214636bc617a61c3e8387628b3415')

_ensure_local_nvm() {
    # let's be sure we are starting clean
    which nvm >/dev/null 2>&1 && nvm deactivate && nvm unload
    export NVM_DIR="${srcdir}/.nvm"

    # The init script returns 3 if version specified
    # in ./.nvrc is not (yet) installed in $NVM_DIR
    # but nvm itself still gets loaded ok
    source /usr/share/nvm/init-nvm.sh || [[ $? != 1 ]]
    nvm install ${_nodeversion}
    nvm use "${_nodeversion}"
}

prepare() {
  cd "${_pkgname}"
  git submodule init
  git submodule update || cd "${srcdir}/${_pkgname}/scripts/resin" && git checkout --
  _ensure_local_nvm
}

build() {
  _ensure_local_nvm
  cd "${_pkgname}"
  export npm_config_build_from_source=true
  export npm_config_cache="${srcdir}/.npm_cache"
  npm install
  npm run package
}

package() {
  cd "${_pkgname}"

  _appdir="${pkgdir}/opt/${pkgname}"
  install -d "${_appdir}"

  cp -a out/balenaEtcher-linux-*/* "${_appdir}"

  install -d "${pkgdir}/usr/bin"
  ln -s /opt/${pkgname}/${pkgname} "${pkgdir}/usr/bin/${pkgname}"
  install -Dm644 "${srcdir}/${pkgname}.desktop" \
    "${pkgdir}/usr/share/applications/${pkgname}.desktop"

  for size in 16x16 32x32 48x48 128x128 256x256 512x512; do
    install -Dm644 "assets/iconset/${size}.png" \
      "${pkgdir}/usr/share/icons/hicolor/${size}/apps/${pkgname}.png"
  done

}

