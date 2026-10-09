# Maintainer: delta-whiplash <delta@delta-net.ovh>

pkgname=bitchord-bin
_appname=BitChord
pkgver=1.8
pkgrel=2
pkgdesc="A modern YouTube Music client with clean aesthetics inspired by Apple Music (prebuilt)"
arch=('x86_64')
url="https://github.com/kushagrasinghx/BitChord"
license=('GPL-3.0-only')
depends=('glibc' 'jq')
optdepends=('mpv: alternative media backend')
provides=('bitchord')
conflicts=('bitchord')
options=('!strip')

source=(
    "${_appname}-${pkgver}-linux-amd64.deb::https://github.com/kushagrasinghx/BitChord/releases/download/v${pkgver}/${_appname}-${pkgver}-linux-amd64.deb"
    "LICENSE-${pkgver}::https://raw.githubusercontent.com/kushagrasinghx/BitChord/main/LICENSE"
)
sha256sums=('b5ac5b568015720ada47f378263e83c7dd6d8232d95fc26ddeb4bd1651db1a54'
            '3972dc9744f6499f0f9b2dbf76696f2ae7ad8af9b23dde66d6af86c9dfb36986')

# The deb is a self-contained jpackage image: /opt/bitchord/{bin,lib}
# with a bundled jlink runtime, so no Java dependency is required.
prepare() {
    bsdtar -xf "${_appname}-${pkgver}-linux-amd64.deb" -C "${srcdir}"
    bsdtar -xf "${srcdir}/data.tar.zst" -C "${srcdir}"
}

package() {
    # ---- Application tree ---------------------------------------
    install -dm755 "${pkgdir}/opt"
    cp -a "${srcdir}/opt/bitchord" "${pkgdir}/opt/bitchord"

    # ---- Wrapper in PATH ----------------------------------------
    # The app is a Compose Desktop (AWT) app: under Wayland it runs via
    # XWayland and, with no XSettings daemon, renders at scale 1 and gets
    # upscaled (blurry). Detect the Hyprland monitor scale and pass it as
    # GDK_SCALE so AWT renders at native resolution.
    install -dm755 "${pkgdir}/usr/bin"
    cat > "${pkgdir}/usr/bin/bitchord" <<'EOF'
#!/bin/sh
if [ -z "$GDK_SCALE" ] && command -v hyprctl >/dev/null 2>&1 && hyprctl monitors >/dev/null 2>&1; then
    GDK_SCALE=$(hyprctl -j monitors 2>/dev/null | jq -r '[.[] | select(.focused == true) | .scale] | first // 1' 2>/dev/null)
    [ -n "$GDK_SCALE" ] && [ "$GDK_SCALE" != "null" ] && export GDK_SCALE
fi
exec /opt/bitchord/bin/BitChord "$@"
EOF
    chmod 755 "${pkgdir}/usr/bin/bitchord"

    # ---- .desktop file ------------------------------------------
    install -dm755 "${pkgdir}/usr/share/applications"
    cat > "${pkgdir}/usr/share/applications/bitchord.desktop" <<EOF
[Desktop Entry]
Name=BitChord
Comment=A modern YouTube Music client with clean aesthetics inspired by Apple Music
Exec=/usr/bin/bitchord
Icon=bitchord
Terminal=false
Type=Application
Categories=Audio;AudioVideo;Music;
EOF

    # ---- Icon ---------------------------------------------------
    install -Dm644 "${srcdir}/opt/bitchord/lib/BitChord.png" \
        "${pkgdir}/usr/share/icons/hicolor/1024x1024/apps/bitchord.png"

    # ---- License ------------------------------------------------
    install -Dm644 "${srcdir}/LICENSE-${pkgver}" \
        "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
