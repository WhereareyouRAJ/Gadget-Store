# Stage 1 : Build Stage
FROM golang:1.24-alpine AS builder

# Setting up Working Directory
WORKDIR /src

# Cache Management, We copy only the dependenies file first.
# Copying dependencies files
COPY go.mod go.sum ./ 

# Downloading dependencies
RUN go mod download

# The Build
COPY . .
#CGO_ENABLED=0 creates a static binary which don't need a linux os to run.
RUN CGO_ENABLED=0 GOOS=linux go build -o /bin/catalog . 

# Stage 2: Final Stage
# Scratch is an empty image of size 0MB, provided by docker.         
FROM scratch

# We copy only the single binary from the builder stage, No Shell, No apt, No users.
COPY --from=builder /bin/catalog /bin/catalog

# Metadata
EXPOSE 8080
ENTRYPOINT ["/bin/catalog"]
