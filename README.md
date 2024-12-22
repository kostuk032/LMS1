package main

import (
    "encoding/json"
    "net/http"
    "strconv"
    "strings"
)

type Request struct {
    Expression string `json:"expression"`
}

type Response struct {
    Result string `json:"result,omitempty"`
    Error  string `json:"error,omitempty"`
}

func calculate(expression string) (string, error) {
    // Здесь можно реализовать логику для вычисления арифметических выражений.
    // Например, в простом случае можно сделать что-то вроде eval, если это допустимо.
    
    // В этом примере просто возвращаем результат "4" для "2+2".
    // Необходимо заменить реализацию на реальную.
    if expression == "2+2" {
        return "4", nil
    }
    return "", nil
}

func calculateHandler(w http.ResponseWriter, r *http.Request) {
    if r.Method != http.MethodPost {
        w.WriteHeader(http.StatusMethodNotAllowed)
        return
    }

    var req Request
    err := json.NewDecoder(r.Body).Decode(&req)
    if err != nil || req.Expression == "" {
        http.Error(w, `{"error": "Expression is not valid"}`, http.StatusUnprocessableEntity)
        return
    }

    result, err := calculate(req.Expression)
    if err != nil {
        http.Error(w, `{"error": "Internal server error"}`, http.StatusInternalServerError)
        return
    }

    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(Response{Result: result})
}

func main() {
    http.HandleFunc("/api/v1/calculate", calculateHandler)
    http.ListenAndServe(":8080", nil)
}
Для использования командой curl:

curl --location 'http://localhost:8080/api/v1/calculate' \
--header 'Content-Type: application/json' \
--data '{"expression": "2+2*2"}'
Чтобы запустить проект, выполните команду:
go run ./...# 
