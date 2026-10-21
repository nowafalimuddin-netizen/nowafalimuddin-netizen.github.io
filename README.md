                ctx.font = '700 14px Orbitron, sans-serif';
                ctx.textAlign = 'center';
                ctx.fillText(alive ? 'ARROWS / SWIPE TO START' : 'GAME OVER · TAP TO RESTART', canvas.width / 2, canvas.height / 2);
            }
        }

        document.addEventListener('keydown', e => {
            const k = { ArrowUp: 'UP', ArrowDown: 'DOWN', ArrowLeft: 'LEFT', ArrowRight: 'RIGHT', w: 'UP', s: 'DOWN', a: 'LEFT', d: 'RIGHT' }[e.key];
            if (!k || !gameVisible) return;
            if (e.key.startsWith('Arrow')) e.preventDefault();
            handleMove(k);
        });

        let tx = 0, ty = 0;
        canvas.addEventListener('touchstart', e => { tx = e.touches[0].clientX; ty = e.touches[0].clientY; }, { passive: true });
        canvas.addEventListener('touchend', e => {
            const dx = e.changedTouches[0].clientX - tx, dy = e.changedTouches[0].clientY - ty;
            if (Math.max(Math.abs(dx), Math.abs(dy)) < 20) { if (!alive) resetGame(); return; }
            handleMove(Math.abs(dx) > Math.abs(dy) ? (dx > 0 ? 'RIGHT' : 'LEFT') : (dy > 0 ? 'DOWN' : 'UP'));
        });
        canvas.addEventListener('click', () => { if (!alive) resetGame(); });

        new IntersectionObserver(es => { gameVisible = es[0].isIntersecting; }, { threshold: 0.3 })
            .observe(document.getElementById('game-section'));
        let acc = 0, lt = performance.now();
        (function gameLoop(t) {
            requestAnimationFrame(gameLoop);
            if (!gameVisible) { lt = t; return; }
            acc += Math.min(t - lt, 250); lt = t;
            while (acc >= 110) { acc -= 110; step(); }
        })(lt);
        resetGame();
    </script>
</body>
</html>
