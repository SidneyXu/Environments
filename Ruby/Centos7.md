



ENV RUBY_MIRROR https://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.4.tar.gz

RUN curl -sSL "$RUBY_MIRROR" -o ruby.tar.gz \
    && mkdir -p /opt/ruby \
    && tar -zxf ruby.tar.gz -C /opt/ruby --strip-components=1 \
    && rm ruby.tar.gz \
    && cd ruby \
    && ./configure --with-opt-dir=/usr/local --enable-shared --enable-option-checking \
    && make && make install


    yum -y install ruby