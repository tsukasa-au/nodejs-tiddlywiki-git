# Maintainer: Greg Darke <arch@me.tsukasa.au>
# Based on config by Daniel Goß <developer@flashsystems.de> (nodejs-tiddlywiki)
_npmname=tiddlywiki
pkgname="nodejs-${_npmname}-git"
pkgver=5.3.4_prerelease.r12001.9e46719b2
pkgrel=1
pkgdesc='TiddlyWiki, a non-linear personal web notebook that anyone can use and keep forever, independently of any corporation. This is the nodejs server edition.'
arch=('any')
url="https://tiddlywiki.com/#TiddlyWiki%20on%20Node.js"
license=('BSD')
depends=('nodejs')
makedepends=('npm' 'jq' 'git')
source=("${_npmname}::git+https://github.com/tsukasa-au/TiddlyWiki5.git#branch=systemd-socket-activation")
sha256sums=('SKIP')

pkgver() {
  cd "$_npmname"
  grep -Po '(?<="version": ").*(?=")' package.json | tr '\n-' '._'
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

package() {
	cd "${srcdir}/${_npmname}"
	npm install -g --cache "${srcdir}/npm-cache" --prefix "${pkgdir}/usr" "git+file://$(pwd)"

	# Fix https://github.com/npm/npm/issues/9359
	find "${pkgdir}" -mindepth 1 -type d -exec chmod 755 {} +

	# Fix https://bugs.archlinux.org/task/63396
	chown -R root:root "${pkgdir}"

	# Remove references to $pkgdir
	find "${pkgdir}" -type f -name package.json -print0 | xargs -r -0 sed -i "/_where/d"

	# Remove references to $srcdir
	local tmppackage="$(mktemp)"
	local pkgjson="${pkgdir}/usr/lib/node_modules/${_npmname}/package.json"
	jq '.|=with_entries(select(.key|test("_.+")|not))' "${pkgjson}" > "${tmppackage}"
	mv "${tmppackage}" "${pkgjson}"
	chmod 644 "${pkgjson}"

	# Install license from github
	install -Dm 644 "${srcdir}/${_npmname}/license" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}

