FROM node:18-alpine

# نحدد مكان الكود
WORKDIR /app

# ننسخ الملفات
COPY start.js .

# ننزل express (direct install بدون package.json)
RUN npm install express

# development mode: ممكن نضيف nodemon كمان
# RUN npm install -g nodemon

# نشغل السيرفر
CMD ["node", "start.js"]

EXPOSE 3000
