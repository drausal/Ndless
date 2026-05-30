
## Apple Silicon (arm64) Native Build Configuration

Native support for Apple Silicon is provided by building the GNU ARM toolchain natively on arm64 hardware. The build script uses the following key flags to ensure compatibility with Nspire hardware:

- **Cross-Compiler Target**: Configured as `arm-none-eabi` for bare-metal ARM deployment.
- **Float Support**: Forced to `--with-float=soft` as the Nspire CPU utilizes software floating-point emulation.
- **Library Features**: Newlib is configured with `--enable-newlib-io-long-long` and `--enable-newlib-io-float` to ensure robust standard C library functionality in your projects.
- **Toolchain Compatibility**: `--enable-multilib` and `--enable-interwork` are enabled to support the various instruction sets and architectures required for different Nspire models.

### Corrected Build Command

Ensure all prerequisite libraries (GMP, MPFR, MPC, zlib) are installed natively via Homebrew:

```bash
GMP_PREFIX="$(brew --prefix gmp)"
MPFR_PREFIX="$(brew --prefix mpfr)"
MPC_PREFIX="$(brew --prefix libmpc)"
ZLIB_PREFIX="$(brew --prefix zlib)"
PYTHON_BIN="$(command -v python3)"
LOG="build_toolchain.log"

cd toolchain && \
env PATH="/opt/homebrew/opt/texinfo/bin:/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin" \
    LDFLAGS="-L${GMP_PREFIX}/lib -L${MPFR_PREFIX}/lib -L${MPC_PREFIX}/lib -L${ZLIB_PREFIX}/lib" \
    CPPFLAGS="-I${GMP_PREFIX}/include -I${MPFR_PREFIX}/include -I${MPC_PREFIX}/include -I${ZLIB_PREFIX}/include" \
    CPATH="${GMP_PREFIX}/include:${MPFR_PREFIX}/include:${MPC_PREFIX}/include:${ZLIB_PREFIX}/include" \
    LIBRARY_PATH="${GMP_PREFIX}/lib:${MPFR_PREFIX}/lib:${MPC_PREFIX}/lib:${ZLIB_PREFIX}/lib" \
    PARALLEL=-j4 \
    PYTHON="${PYTHON_BIN}" \
    ./build_toolchain.sh > "$LOG" 2>&1
```