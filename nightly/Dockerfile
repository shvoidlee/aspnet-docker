FROM mono:3.12

ENV DNX_FEED https://www.myget.org/F/aspnetvnext/api/v2
ENV DNX_USER_HOME /opt/dnx

RUN apt-get -qq update && apt-get -qqy install unzip

ONBUILD RUN curl -sSL https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.sh | DNX_BRANCH=dev sh
ONBUILD RUN bash -c "source $DNX_USER_HOME/dnvm/dnvm.sh \
	&& dnvm install latest -a default \
	&& dnvm alias default | xargs -i ln -s $DNX_USER_HOME/runtimes/{} $DNX_USER_HOME/runtimes/default"

# Install libuv for Kestrel from source code (binary is not in wheezy and one in jessie is still too old)
RUN apt-get -qqy install \
	autoconf \
	automake \
	build-essential \
	libtool
RUN LIBUV_VERSION=1.0.0-rc2 \
	&& curl -sSL https://github.com/joyent/libuv/archive/v${LIBUV_VERSION}.tar.gz | tar zxfv - -C /usr/local/src \
	&& cd /usr/local/src/libuv-$LIBUV_VERSION \
	&& sh autogen.sh && ./configure && make && make install \
	&& rm -rf /usr/local/src/libuv-$LIBUV_VERSION \
	&& ldconfig

COPY NuGet.Config /tmp/
RUN mkdir -p $HOME/.config/NuGet/ && mv /tmp/NuGet.Config $HOME/.config/NuGet/

ENV PATH $PATH:$DNX_USER_HOME/runtimes/default/bin
