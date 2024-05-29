```js
server: {
    proxy: {
        "/api": {
            target: "http://127.0.0.1:3000/",
            changeOrigin: true,
            secure: false,
            rewrite: (path) => path.replace(/^\/api/, ""),
        },
    },
    host: "0.0.0.0"
}
```

[[vite]]
[[proxy]]