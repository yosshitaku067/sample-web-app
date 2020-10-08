# Dockerfile ではソースのコピーとビルド処理を実行する

##################### angular build ##################################################
### ベースステージ ###
FROM node:lts-alpine as angular

# @angular/cliをグローバルインストール
RUN npm install -g @angular/cli

# 全てのソースコードをコピー
COPY ./frontend-app /frontend-app/

# ワーキングディレクトリの設定
WORKDIR /frontend-app

# 一度 node_modules を削除してからnpm install
RUN npm install

# プロダクションビルド
RUN ng build --prod
######################################################################################

##################### express build ##################################################
### ビルドステージ ###
FROM node:lts-alpine as express

# 全てのソースコードをコピー
COPY ./backend-app /backend-app/

# ワーキングディレクトリの設定
WORKDIR /backend-app

# 一度 node_modules を削除してからnpm install
RUN npm install

# バックエンドビルド
RUN npm run build
######################################################################################

##################### express build ##################################################
### 実行用コンテナ ###
FROM node:lts-alpine

EXPOSE 3000

# ビルド済みバックエンドをコピー
COPY --from=express /backend-app/dist /dist

# ビルド済みangularサイトをコピー
COPY --from=angular /frontend-app/dist/frontend-app /www

# package.jsonをコピー
COPY --from=express /backend-app/package*.json /

# dependencies のパッケージのみinstall
RUN npm i --production

ENTRYPOINT ["node", "dist/main.js"]
######################################################################################
