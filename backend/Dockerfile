FROM ruby:3.3.0

# Node.js 20.x LTS をインストール
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get install -y nodejs

# 必要なパッケージをインストール
RUN apt-get update -qq && apt-get install -y \
    build-essential \
    libpq-dev \
    postgresql-client \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /rails

# Gemfileをコピーしてbundle install
COPY Gemfile Gemfile.lock* ./
RUN bundle config set --local path 'vendor/bundle' && bundle install

# アプリケーションコードをコピー
COPY . .

# docker-entrypointスクリプトに実行権限を設定
RUN chmod +x /rails/bin/docker-entrypoint

EXPOSE 3000

ENTRYPOINT ["/rails/bin/docker-entrypoint"]
CMD ["./bin/rails", "server", "-b", "0.0.0.0"]
