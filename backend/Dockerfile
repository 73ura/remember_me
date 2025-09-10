FROM ruby:3.3.0

# Node.js 20.x LTS をインストール
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get install -y nodejs

# 必要なパッケージをインストール
RUN apt-get update -qq && apt-get install -y \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Gemfileをコピーしてbundle install
COPY Gemfile Gemfile.lock* ./
RUN bundle config set --local path 'vendor/bundle' && bundle install

# アプリケーションコードをコピー
COPY . .

EXPOSE 3000

ENTRYPOINT ["bundle", "exec"]
CMD ["rails", "server", "-b", "0.0.0.0"]