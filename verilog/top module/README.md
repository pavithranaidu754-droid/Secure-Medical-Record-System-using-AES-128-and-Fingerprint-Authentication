Verilog source files for the AES-128 implementation
#Top module 
module top_system(
    input clk,
    input rst,
    input uart_rx,
    output uart_tx,
    output reg [15:0] LED,
    output reg [6:0] seg,
    output reg [3:0] an
);

parameter CLKS_PER_BIT = 100_000_000 / 9600;

//////////////////// UART RX ////////////////////
reg [15:0] clk_cnt = 0;
reg [3:0] bit_index = 0;
reg [9:0] shift_reg = 0;
reg receiving = 0;

reg [7:0] rx_byte;
reg rx_done = 0;

always @(posedge clk) begin
    rx_done <= 0;

    if (rst) begin
        receiving <= 0;
        clk_cnt <= 0;
        bit_index <= 0;
    end else begin

        if (!receiving && uart_rx == 0) begin
            receiving <= 1;
            clk_cnt <= CLKS_PER_BIT/2;
            bit_index <= 0;
        end

        else if (receiving) begin
            if (clk_cnt == CLKS_PER_BIT-1) begin
                clk_cnt <= 0;
                shift_reg[bit_index] <= uart_rx;
                bit_index <= bit_index + 1;

                if (bit_index == 9) begin
                    receiving <= 0;
                    rx_byte <= shift_reg[8:1];
                    rx_done <= 1;
                end
            end else begin
                clk_cnt <= clk_cnt + 1;
            end
        end
    end
end

//////////////////// EDGE ////////////////////
reg rx_done_d;
wire rx_pulse;

always @(posedge clk)
    rx_done_d <= rx_done;

assign rx_pulse = rx_done & ~rx_done_d;

//////////////////// UART TX ////////////////////
reg tx_start;
reg [7:0] tx_data;
wire tx_busy;

uart_tx tx_inst(
    .clk(clk),
    .start(tx_start),
    .data(tx_data),
    .tx(uart_tx),
    .busy(tx_busy)
);

//////////////////// DISPLAY ////////////////////
reg [3:0] display_code;

always @(*) begin
    case(display_code)
        4'd1: seg = 7'b0001000; // A (Access)
        4'd2: seg = 7'b0001110; // F (Fail)
        default: seg = 7'b1111111;
    endcase
end

always @(*) an = 4'b1110;

//////////////////// MAIN LOGIC ////////////////////
always @(posedge clk) begin
    if (rst) begin
        tx_start <= 0;
        LED <= 0;
        display_code <= 0;
    end else begin

        tx_start <= 0;

        if (rx_pulse && !tx_busy) begin

            // VALID USERS (1-10)
            if (rx_byte >= 1 && rx_byte <= 10) begin

                LED <= 16'b1000000000000000; // success
                display_code <= 1;

                tx_data <= rx_byte; // send to Python
                tx_start <= 1;

            end else begin

                LED <= 16'b0100000000000000; // fail
                display_code <= 2;

                tx_data <= 8'hFF;
                tx_start <= 1;
            end
        end
    end

    
end

endmodule
