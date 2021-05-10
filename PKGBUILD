# Maintainer: kageru <kageru@encode.moe>
# Maintainer: Sam Whited <sam@samwhited.com>
# Contributor: Francois Menning <f.menning@pm.me>
# Contributor: Anton Kudryavtsev <anton@anibit.ru>
# Contributor: Frederik Schwan <frederik dot schwan at linux dot com>
# Contributor: Thomas Fanninger <thomas@fanninger.at>
# Contributor: Alexander F Rødseth <xyproto@archlinux.org>
# Contributor: Thomas Laroche <tho.laroche@gmail.com>

_pkgname='gitea'
pkgname=gitea-git
pkgver=v1.16.0_dev_122_g4aa3cacc4
pkgrel=1
pkgdesc='Painless self-hosted Git service. Community managed fork of Gogs.'
arch=('x86_64' 'i686' 'arm' 'armv6h' 'armv7h' 'aarch64')
url='https://gitea.io/'
license=('MIT')
depends=('git')
makedepends=('go' 'npm')
optdepends=('mariadb: MariaDB support'
            'memcached: MemCached support'
            'openssh: GIT over SSH support'
            'pam: Authentication via PAM support'
            'postgresql: PostgreSQL support'
            'redis: Redis support'
            'sqlite: SQLite support')
backup=('etc/gitea/app.ini')
conflicts=('gitea')
provides=('gitea')
source=(git+https://github.com/go-gitea/gitea.git
        gitea.tmpfiles
        gitea.service
        gitea.sysusers
        gitea-arch-defaults.patch)
sha512sums=('SKIP'
            '89bf119a91fd48ed35c06131c67de1b4300bd2e79522c47aee9a73d7f1ebb08d9bceadc37408bd2425475d92c8bf59d87a799f2ce0a46bee860bf9fc7a904103'
            '6a59adf37e81cdd3e69b44e4812b90fb387f5250cbfacd2cba533a72d852e7e32419d1022dde29c4d94674a71719a09c081fa5d1129bdd983c844bd778469e97'
            '77f672ed82bc8f78ca04b1e2b7c7d026cb897da6e4f057817adbe1242bf8a67875061553806e6b027cdb3266cdf217ee3993efd9242a66c5802ed34344b5ded1'
            'dd664e04c4268455dab80bb9473624c60bd81d2214c4caddee4e42c69652a68b7bc811698d3a064ece6f9279b96e58f5367081deda0413f137d82e6b81cb0fc8')
install=gitea.install

pkgver() {
  cd "${srcdir}/${_pkgname}"
  git describe --tags --long | sed s/-/_/g
}

prepare() {
  cd ${srcdir}/${_pkgname}
  # Change default repos path for ArchLinux and some additional settings
  patch -Np1 -i ../gitea-arch-defaults.patch
  # Be nice to people with read-only home
  GOCACHE="${srcdir}/cache" make vendor
}

build() {
  cd ${srcdir}/${_pkgname}
  # Again, be nice to people with read-only home
  export GOCACHE="${srcdir}/cache"
  export CGO_CPPFLAGS="${CPPFLAGS}"
  export CGO_CFLAGS="${CFLAGS}"
  export CGO_CXXFLAGS="${CXXFLAGS}"
  export CGO_LDFLAGS="${LDFLAGS}"
  export LDFLAGS="-X 'code.gitea.io/gitea/modules/setting.AppWorkPath=/var/lib/gitea/'"
  export EXTRA_GOFLAGS="-buildmode=pie -trimpath -mod=readonly -modcacherw"
  export TAGS="bindata sqlite sqlite_unlock_notify pam"
  make -j1 # building in parallel breaks the bindata target which relies on execution order
}

package() {
  install -Dm755 ${_pkgname}/${_pkgname} -t "${pkgdir}"/usr/bin/
  install -Dm644 ${_pkgname}/LICENSE -t "${pkgdir}"/usr/share/licenses/${pkgname}/
  install -Dm644 ${_pkgname}.service -t "${pkgdir}"/usr/lib/systemd/system/
  install -Dm644 ${_pkgname}.tmpfiles "${pkgdir}"/usr/lib/tmpfiles.d/${_pkgname}.conf
  install -Dm644 ${_pkgname}.sysusers "${pkgdir}"/usr/lib/sysusers.d/${_pkgname}.conf
  install -D ${_pkgname}/custom/conf/app.example.ini "${pkgdir}"/etc/gitea/app.ini
}

# vim: ts=2 sw=2 et:
