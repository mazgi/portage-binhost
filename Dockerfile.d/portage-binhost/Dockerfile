FROM ghcr.io/mazgi/portage-binhost as cache
FROM gentoo/portage as portage
FROM gentoo/stage3 as builder
ARG GIT_SHORT_SHA
COPY --from=cache /usr/share/nginx/html/var/cache/binpkgs /var/cache/binpkgs
COPY --from=portage /var/db/repos/gentoo /var/db/repos/gentoo
COPY rootfs /

RUN :\
  && eselect news read all\
  && tar cf /tmp/portage-settings.tar.gz --no-same-owner --no-same-permissions /etc/portage\
  && emerge --buildpkg --usepkg -uDNqv @world\
  && sed -i -e '/^IMAGE_ID=/d;$aIMAGE_ID='${GIT_SHORT_SHA} /etc/os-release\
  && :

FROM nginx:alpine as binhost
ARG GIT_SHORT_SHA
COPY --from=builder /tmp/portage-settings.tar.gz /usr/share/nginx/html
COPY --from=builder /var/cache/binpkgs /usr/share/nginx/html/var/cache/binpkgs
RUN sed -i -e '/^IMAGE_ID=/d;$aIMAGE_ID='${GIT_SHORT_SHA} /etc/os-release
