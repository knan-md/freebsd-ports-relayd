PORTNAME=	relayd
DISTVERSION=	7.3.20230512
PORTREVISION=	0
CATEGORIES=	net

MAINTAINER=	dch@FreeBSD.org
COMMENT=	OpenBSD relay daemon
WWW=		https://github.com/klaraSystems/freebsd-relayd

LICENSE=	ISCL

USES=		ssl:build uidfix
BROKEN_SSL=	base boringssl libressl openssl openssl30 openssl31
BROKEN_SSL_REASON=	Requires LibreSSL-devel 3.7.2 or newer
USE_GITHUB=	yes
GH_ACCOUNT=	dch
GH_TAGNAME=	f8038bfa488ad3cf9d43055ed79cb56d0c38e75c
USE_RC_SUBR=	relayd

MAKE_ARGS+=	BINDIR=${PREFIX}/sbin \
		MANDIR=${PREFIX}/man/man
CFLAGS+=	-Wall -pthread

USERS=		_relayd
GROUPS=		_relayd

.include <bsd.port.pre.mk>

.if !${SSL_DEFAULT:Mlibressl*}
. ifnmake describe
STAGEDIR_libressl!=	${MAKE} -V STAGEDIR -C ${PORTSDIR}/security/libressl
. endif
BUILD_DEPENDS+=		${NONEXISTENT}:security/libressl:stage
.endif # SSL_DEFAULT

CFLAGS+=	-I${STAGEDIR_libressl}${LOCALBASE}/include

post-patch:
	${REINPLACE_CMD} -e 's|%%PREFIX%%|${PREFIX}|g' \
		${WRKSRC}/usr.sbin/relayd/relay.c \
		${WRKSRC}/usr.sbin/relayd/relayd.h \
		${WRKSRC}/usr.sbin/relayd/relayd.conf.5 \
		${WRKSRC}/usr.sbin/relayd/relayd.8

do-configure:
	(cd ${WRKSRC} && \
		./configure)

do-install:
	${INSTALL_DATA} ${WRKSRC}/etc/examples/relayd.conf \
		${STAGEDIR}${PREFIX}/etc/relayd.conf.sample
	${INSTALL_MAN} ${WRKSRC}/usr.sbin/relayctl/relayctl.8 \
		${STAGEDIR}${MANPREFIX}/man/man8/
	${INSTALL_MAN} ${WRKSRC}/usr.sbin/relayd/relayd.8 \
		${STAGEDIR}${MANPREFIX}/man/man8/
	${INSTALL_MAN} ${WRKSRC}/usr.sbin/relayd/relayd.conf.5 \
		${STAGEDIR}${MANPREFIX}/man/man5/
	${INSTALL_PROGRAM} ${WRKSRC}/usr.sbin/relayctl/relayctl \
		${STAGEDIR}${PREFIX}/sbin/
	${INSTALL_PROGRAM} ${WRKSRC}/usr.sbin/relayd/relayd \
		${STAGEDIR}${PREFIX}/sbin/

post-install:
	@${STRIP_CMD} ${STAGEDIR}${PREFIX}/sbin/relay*

.include <bsd.port.post.mk>
