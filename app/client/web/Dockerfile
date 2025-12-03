FROM node:22-alpine AS base
WORKDIR /app
ENV NODE_ENV=production

FROM base AS deps
RUN corepack enable

COPY package.json pnpm-lock.yaml ./

# 설치 (CI에서라면 --frozen-lockfile 추천)
RUN pnpm install --frozen-lockfile

FROM base AS builder
RUN corepack enable

WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules

COPY . .

RUN pnpm build

FROM base AS runner
WORKDIR /app
RUN corepack enable

ENV PORT=3000
ENV NODE_ENV=production

COPY --from=builder /app/public ./public
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/package.json ./package.json
COPY --from=deps    /app/node_modules ./node_modules
# copy next.config.ts / next.config.mjs
COPY next.config.* ./

EXPOSE 3000

CMD ["pnpm", "start"]
