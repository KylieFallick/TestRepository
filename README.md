# TestRepository
Test Repository

create or replace package body cdw_apps.all_question is

function get_orig_scale_value_id(i_scale_id         in number,
                                   i_calculated_value in number,
                                   i_request_run_id   in number default null)
    return number is

    -- local variable for input parameters
    v_scale_id         number;
    v_calculated_value number;

    -- Error handling variables
    v_error_message  varchar2(4000);
    v_request_run_id number;
    -- Exceptions
    v_null_parameter_exception exception;

    -- Return variable
    o_return_value number;

  begin
    -- Assigning values to local variables
    v_scale_id         := i_scale_id;
    v_calculated_value := i_calculated_value;
    v_request_run_id   := i_request_run_id;

    begin

      v_error_message := 'all_question.get_orig_scale_value_id request_run_id:' ||
                         v_request_run_id || ' Scale id: ' || v_scale_id ||
                         ' Calculated value: ' || v_calculated_value ||
                         chr(10);

      if v_scale_id is null or v_calculated_value is null then
        raise v_null_parameter_exception;
      end if;

      select distinct s.orig_scale_value_id
        into o_return_value
        from sharedim.all_scales s
       where s.scale_id = v_scale_id
         and s.calculated_value = v_calculated_value;

    exception

      when v_null_parameter_exception then
        v_error_message := v_error_message ||
                           ' Null parameter(s) found. Exception! - ' ||
                           to_char(sysdate, 'MM/DD/YYYY HH:MI:SS') ||
                           chr(10);
        cdw_apps.request_management.write_file(v_request_run_id,
                                               v_error_message,
                                               -20001,
                                               'Null parameter(s) found in all_question.get_orig_scale_value_id');

      when no_data_found then
        v_error_message := v_error_message ||
                           ' No Data Found Exception! - ' ||
                           to_char(sysdate, 'MM/DD/YYYY HH:MI:SS') ||
                           chr(10);
        cdw_apps.request_management.write_file(v_request_run_id,
                                               v_error_message,
                                               -20001,
                                               'No Data Found exception in all_question.get_orig_scale_value_id');

      when too_many_rows then
        v_error_message := v_error_message ||
                           ' Too Many Rows Found Exception! - ' ||
                           to_char(sysdate, 'MM/DD/YYYY HH:MI:SS') ||
                           chr(10);
        cdw_apps.request_management.write_file(v_request_run_id,
                                               v_error_message,
                                               -20001,
                                               'Too Many Rows Found exception in all_question.get_orig_scale_value_id');

      when others then
        v_error_message := v_error_message ||
                           ' Error in all_question.get_orig_scale_value_id. ' ||
                           sqlerrm || '-' ||
                           to_char(sysdate, 'MM/DD/YYYY HH:MI:SS') ||
                           chr(10);
        cdw_apps.request_management.write_file(v_request_run_id,
                                               v_error_message,
                                               -20001,
                                               'Unhandled exception in all_question.get_orig_scale_value_id');
    end;
    return o_return_value;
  end;
